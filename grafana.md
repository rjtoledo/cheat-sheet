# grafana.md

1. Install Grafana using the following commands:

    ```
    
    wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
    echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    sudo apt update
    sudo apt install grafana
    ```

2. Start the Grafana service using the following command:

    ```
    
    sudo systemctl start grafana-server
    ```

3. Verify that Grafana is running by accessing the web interface at **`http://localhost:3000`**.

## Adding Data Sources

### Prometheus

1. Configure Prometheus as a data source in Grafana by following these steps:

    a. Log in to the Grafana web interface at **`http://localhost:3000`**.

    b. Click on the gear icon in the left sidebar and select "Data Sources".

    c. Click on the "Add data source" button and select "Prometheus".

    d. In the "HTTP" section, set the URL to **`http://localhost:9090`**.

    e. Click on the "Save & Test" button to test the connection to Prometheus.

2. Import a dashboard to visualize Prometheus metrics in Grafana by following these steps:

    a. Go to the Grafana dashboard website at **`https://grafana.com/grafana/dashboards`**.

    b. Search for a dashboard that suits your needs, and copy its dashboard ID.

    c. In the Grafana web interface, click on the plus sign in the left sidebar and select "Import".

    d. Paste the dashboard ID into the "Grafana.com Dashboard" field.

    e. Follow the remaining steps to import the dashboard and configure it to your needs.

## SSL Installation

I add SSL to my Grafana web server to ensure all traffic is encrypted between the server and web browser. I use LetsEncrypt by following the [Certbot](https://certbot.eff.org/) instructions. For **Web Server software**, I choose **None of the Above** For **Operating System**, I choose **Ubuntu 20.04 LTS**

I then SSH onto my new Grafana server,

I enter these commands

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo apt-get update
```

I then install Certbot with the Nginx option

```
sudo apt-get install certbot
```

then run

```
sudo certbot certonly --standalone
```

According to several forums on adding SSL to Grafana, the Grafana service may have permission problems finding the SSL certificates that were just installed.

So copy the new `*.pem` files from the`/etc/letsencrypt/live/**YOUR-DOMAIN-NAME**`

folder to the `/etc/grafana` folder.

Be careful to replace **`YOUR-DOMAIN-NAME`** with your actual domain name in the command below.

```
cp /etc/letsencrypt/live/YOUR-DOMAIN-NAME/*.pem /etc/grafana/
```

Now cd into the `/etc/grafana` folder and view the contents.

```
cd /etc/grafana/
ls -lh
```

After copying the `*.pem` files to the `/etc/grafana` folder, they will also need their owner and access permissions edited in order for the Grafana server process to read them.

Change the owner of the specific `fullchain.pem` and `privkey.pem` files to the `grafana` group

```
chown :grafana /etc/grafana/fullchain.pem
chown :grafana /etc/grafana/privkey.pem
```

Then allow the `grafana` group read access to the files.

```
chmod 640 /etc/grafana/fullchain.pem
chmod 640 /etc/grafana/privkey.pem
```

Now edit the `grafana.ini` file

```
sudo nano grafana.ini
```

Uncomment and Change the line

```
# Protocol (http, https, h2, socket)
;protocol = http
```

to

```
# Protocol (http, https, h2, socket)
protocol = https
```

and uncomment and change

```
# https certs & key file
;cert_file =
;cert_key =
```

to

```
# https certs & key file
cert_file = /etc/grafana/fullchain.pem
cert_key = /etc/grafana/privkey.pem
```

I then restart the Grafana server,

```
sudo service grafana-server restart
```

and check its status

```
sudo service grafana-server status
```

## SSL Auto Renewal

1. Install the Certbot utility by running the following command:

    ```
    
    sudo apt install certbot
    ```

2. Generate a new SSL certificate for your Grafana installation using the Certbot command:

    ```
    
    sudo certbot certonly --standalone -d your-domain.com
    ```

    Replace "your-domain.com" with the actual domain name of your Grafana server.

3. Verify that the SSL certificate was installed successfully by checking the output of the Certbot command. The output should contain a message that says "Congratulations! Your certificate and chain have been saved at".
4. Create a cron job to run the renewal command twice a day:

    ```
    
    sudo crontab -e
    ```

    Add the following line to the crontab file:

    ```
    
    0 0,12 * * * /usr/bin/certbot renew --quiet
    ```

    This will run the renewal command every day at 12:00 AM and 12:00 PM. If the certificate is due for renewal, the command will automatically renew it.

5. Save and exit the crontab file.
