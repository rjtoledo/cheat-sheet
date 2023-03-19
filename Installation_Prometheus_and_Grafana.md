# Installation Prometheus and Grafana

To install Prometheus and Grafana on Ubuntu 21.10, you can follow these steps:

1. Install Prometheus using the following commands:
    
    ```
    
    sudo apt update
    sudo apt install prometheus
    ```
    
2. Verify that Prometheus is running by accessing the web interface at **`http://localhost:9090`**.
3. Install Grafana using the following commands:
    
    ```
    
    wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
    echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    sudo apt update
    sudo apt install grafana
    ```
    
4. Start the Grafana service using the following command:
    
    ```
    
    sudo systemctl start grafana-server
    ```
    
5. Verify that Grafana is running by accessing the web interface at **`http://localhost:3000`**.
6. Configure Prometheus as a data source in Grafana by following these steps:
    
    a. Log in to the Grafana web interface at **`http://localhost:3000`**.
    
    b. Click on the gear icon in the left sidebar and select "Data Sources".
    
    c. Click on the "Add data source" button and select "Prometheus".
    
    d. In the "HTTP" section, set the URL to **`http://localhost:9090`**.
    
    e. Click on the "Save & Test" button to test the connection to Prometheus.
    
7. Import a dashboard to visualize Prometheus metrics in Grafana by following these steps:
    
    a. Go to the Grafana dashboard website at **`https://grafana.com/grafana/dashboards`**.
    
    b. Search for a dashboard that suits your needs, and copy its dashboard ID.
    
    c. In the Grafana web interface, click on the plus sign in the left sidebar and select "Import".
    
    d. Paste the dashboard ID into the "Grafana.com Dashboard" field.
    
    e. Follow the remaining steps to import the dashboard and configure it to your needs.