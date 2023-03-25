# Prometheus

To install Prometheus and Grafana on Ubuntu 21.10, you can follow these steps:

1. Install Prometheus using the following commands:

    ```
    
    sudo apt update
    sudo apt install prometheus
    ```

2. Verify that Prometheus is running by accessing the web interface at **`http://localhost:9090`**.

## Troubleshooting

If you see the message "Ruler API not supported" when configuring Prometheus as a data source in Grafana, it means that the Ruler API is not enabled in your Prometheus installation.

To enable the Ruler API in Prometheus, you need to add the following configuration to your **`prometheus.yml`** file:

```

rule_files:
  - /path/to/rules/*.rules
```

This configuration specifies the location of the rule files that Prometheus should use to evaluate alerts. Once you have added this configuration and restarted Prometheus, the Ruler API should be available and you can configure alerts in Grafana using the Alerting UI.

You can also check the Prometheus logs to see if there are any errors or warnings that may be preventing the Ruler API from working correctly.
