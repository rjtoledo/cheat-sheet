# Json Exporter Setup

If you are getting the "Unit not found" error while trying to start the **`json_exporter`** service, it means that the systemd service for the exporter is not installed or not installed properly.

To install the **`json_exporter`** service, you can follow these steps:

1. Download the latest version of the **`json_exporter`** binary from the official GitHub repository: **[https://github.com/prometheus-community/json_exporter/releases](https://github.com/prometheus-community/json_exporter/releases)**
2. Extract the binary file from the downloaded archive and move it to the **`/usr/local/bin/`** directory:

```

tar xvzf json_exporter-X.X.X.linux-amd64.tar.gz
sudo mv json_exporter-X.X.X.linux-amd64/json_exporter /usr/local/bin/

```

Make sure to replace "X.X.X" with the version number you downloaded.

1. Create a systemd service file for the **`json_exporter`**:

```

sudo nano /etc/systemd/system/json_exporter.service

```

1. Paste the following configuration into the service file:

```
[Unit]
Description=JSON Exporter
After=network.target

[Service]
User=yourusername
Group=yourgroupname
ExecStart=/usr/local/bin/json_exporter --config.file /etc/json_exporter/config.yml

[Install]
WantedBy=multi-user.target
```

1. Save and close the file.
2. Reload the systemd daemon to load the new service file:

```

sudo systemctl daemon-reload

```

And enable the service to start on boot:

```

sudo systemctl enable json_exporter.service
```

1. Start the **`json_exporter`** service:

```

sudo systemctl start json_exporter

```

1. Verify that the service is running without any errors:

```

sudo systemctl status json_exporter

```

If everything is working fine, you should see a message indicating that the service is active and running.

## Troubleshooting

1. Make sure the **`config.yml`** file is in the same directory where you're running the **`json_exporter`** command from.
2. Make sure you're running the **`json_exporter`** with the correct command, such as:

```

/usr/local/bin/ ./json_exporter --config.file=/etc/json_exporter/config.yml

```

You should be able to access the metrics scraped by the exporter at **`http://localhost:7979/metrics`**

1. Make sure the **`json_exporter`** binary has executable permissions. You can check this by running:

```

ls -l json_exporter

```

The output should include **`-rwxr-xr-x`** or something similar, indicating that the file is executable.

1. Make sure that the **`metrics`** section in your **`config.yml`** matches the format of the JSON output that you're trying to export. You can test this by running the **`json_exporter`** with the **`-web.listen-address`** flag and accessing the **`/metrics`** endpoint in a web browser. If the endpoint returns data, then the **`json_exporter`** is working correctly.

## Enable TLS

To enable TLS on your json_exporter, you will need to generate a TLS certificate and key, and then configure the exporter to use them.

Here are the general steps:

1. Generate a TLS certificate and key:

```

openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout json_exporter.key -out json_exporter.crt
```

1. Move the certificate and key to a secure location, for example **`/etc/json_exporter/ssl`**.

```

sudo mkdir /etc/json_exporter/ssl
sudo mv json_exporter.key json_exporter.crt /etc/json_exporter/ssl/
```

1. Modify your **`config.yml`** file to include the TLS configuration. Add the following section to the bottom of the file:

```
tls:
  cert_file: /etc/json_exporter/ssl/json_exporter.crt
  key_file: /etc/json_exporter/ssl/json_exporter.key
```

1. Restart the json_exporter service to apply the changes.

```

sudo systemctl restart json_exporter.service
```

1. Update your Prometheus configuration file to include the target URL with the **`https`** protocol and the proper port. For example:

```

- job_name: json
    metrics_path: /probe
    scheme: https
    static_configs:
      - targets:
        - localhost:7979
```

1. Reload your Prometheus configuration to apply the changes.

```

sudo systemctl reload prometheus.service
```

With these steps, your json_exporter should now be serving metrics over HTTPS and your Prometheus server should be able to scrape them.
