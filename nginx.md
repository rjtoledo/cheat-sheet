# Nginx Setup

1. Update the system: Before installing Nginx, it is important to update your system to ensure you have the latest security patches and software versions. You can do this by running the following command:

    ```
    
    sudo apt-get update && sudo apt-get upgrade
    ```

2. Install Nginx: Once the system is updated, you can install Nginx by running the following command:

    ```
    
    sudo apt-get install nginx
    ```

3. Verify that Nginx is running: After installation, you can verify that Nginx is running by checking its status with the following command:

    ```
    
    sudo systemctl status nginx
    ```

    If Nginx is running properly, you should see a green "Active (running)" message in the output.

4. Configure Nginx: Nginx configuration files are located in the /etc/nginx directory. The main configuration file is nginx.conf. You can modify this file to suit your needs. For example, to add a new server block to serve a website, you can create a new configuration file in the /etc/nginx/sites-available directory and then create a symbolic link to the /etc/nginx/sites-enabled directory. Here's an example:

    ```
    
    sudo nano /etc/nginx/sites-available/example.com
    ```

    Add the following configuration to the file:

    ```
    
    server {
        listen 80;
        server_name example.com www.example.com;
        root /var/www/example.com;
        index index.html;
    }
    ```

    Then, create a symbolic link:

    ```
    
    sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
    ```

    Finally, restart Nginx to apply the changes:

    ```
    
    sudo systemctl restart nginx
    ```

5. Test the configuration: Once you've created a server block and restarted Nginx, you can test the configuration by visiting the website in a browser. In this example, you would visit **[http://example.com](http://example.com/)** to verify that the website is being served correctly.

That's it! These are the basic steps to set up Nginx on your system. Of course, there are many other configuration options you can explore as well, but this should give you a good starting point.

## Adding Multiple Virtual Host

Here's an example of how you can set up two virtual hosts:

1. Create a directory to hold the configuration files for your virtual hosts:

    ```
    
    sudo mkdir /etc/nginx/sites-available
    sudo mkdir /etc/nginx/sites-enabled
    ```

2. Create a configuration file for your first virtual host:

    ```
    
    sudo nano /etc/nginx/sites-available/example1.com
    ```

    Add the following configuration to the file:

    ```
    
    server {
        listen 80;
        server_name example1.com www.example1.com;
        root /var/www/example1.com;
        index index.html;
    }
    ```

3. Create a configuration file for your second virtual host:

    ```
    
    sudo nano /etc/nginx/sites-available/example2.com
    ```

    Add the following configuration to the file:

    ```
    
    server {
        listen 80;
        server_name example2.com www.example2.com;
        root /var/www/example2.com;
        index index.html;
    }
    ```

4. Create symbolic links for the two configuration files in the **`sites-enabled`** directory:

    ```
    
    sudo ln -s /etc/nginx/sites-available/example1.com /etc/nginx/sites-enabled/
    sudo ln -s /etc/nginx/sites-available/example2.com /etc/nginx/sites-enabled/
    ```

    This enables the virtual hosts to be active.

5. Verify the configuration:

    ```
    sudo nginx -t
    ```

    If the configuration is valid, you will see a message indicating that the syntax is OK.

6. Restart Nginx to apply the changes:

    ```
    
    sudo systemctl restart nginx
    ```

    Now you should be able to visit **`http://example1.com`** and **`http://example2.com`** in your browser to verify that the virtual hosts are serving pages correctly.

That's it! You can add as many virtual hosts as you need by creating additional configuration files in the **`sites-available`** directory and creating symbolic links in the **`sites-enabled`** directory.

## Installing SSL using Lets Encrypt

1. Install certbot:

    ```
    
    sudo apt-get update
    sudo apt-get install certbot
    ```

2. Obtain SSL certificates:

    ```
    
    sudo certbot certonly --webroot -w /var/www/html -d example.com -d www.example.com
    ```

    Replace **`example.com`** with your domain name. This command will create a new SSL certificate for your domain.

3. Edit Nginx configuration file:

    ```
    
    sudo nano /etc/nginx/sites-available/example.com
    ```

    Add the following lines inside the server block:

    ```
    
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    ```

    Replace **`example.com`** with your own domain name.

4. Test Nginx configuration:

    ```
    sudo nginx -t
    ```

    If there are no syntax errors, restart Nginx to apply the changes:

    ```
    
    sudo systemctl restart nginx
    ```

5. Automate SSL renewal:

    Let's Encrypt SSL certificates are valid for 90 days, so it's important to renew them before they expire. You can automate the renewal process by creating a cron job that runs the following command:

    ```
    
    sudo certbot renew
    ```

    This command will check if any certificates are due for renewal and renew them if necessary. To create the cron job, run the following command:

    ```
    
    sudo crontab -e
    ```

    Then add the following line at the end of the file:

    ```
    
    0 0 * * * certbot renew
    ```

    This will run the renewal command every day at midnight.

That's it! Your Nginx server should now be configured to use SSL certificates from Let's Encrypt, and the certificates will be automatically renewed when they expire.

## Installing SSL for multiple vHost

To apply SSL for multiple virtual hosts with Let's Encrypt, you need to obtain a separate SSL certificate for each domain name and configure Nginx to use the appropriate certificate for each virtual host. Here's how you can do it:

1. Install Certbot:

    ```
    
    sudo apt-get update
    sudo apt-get install certbot
    ```

2. Obtain SSL certificates for each domain:

    ```
    
    sudo certbot certonly --webroot -w /var/www/html -d example.com -d www.example.com
    sudo certbot certonly --webroot -w /var/www/html -d example2.com -d www.example2.com
    ```

    This command will create SSL certificates for **`example.com`** and **`example2.com`**. You can add additional **`-d`** options to include more domain names.

3. Edit Nginx configuration file for the first virtual host:

    ```
    
    sudo nano /etc/nginx/sites-available/example.com
    ```

    Add the following lines inside the server block:

    ```
    
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    ```

    Replace **`example.com`** with your own domain name.

4. Edit Nginx configuration file for the second virtual host:

    ```
    
    sudo nano /etc/nginx/sites-available/example2.com
    ```

    Add the following lines inside the server block:

    ```
    
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example2.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example2.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    ```

    Replace **`example2.com`** with your own domain name.

5. Test Nginx configuration:

    ```
    
    sudo nginx -t
    ```

    If there are no syntax errors, restart Nginx to apply the changes:

    ```
    
    sudo systemctl restart nginx
    ```

6. Automate SSL renewal:

    To automate SSL renewal, create a cron job for the Certbot renewal command as explained in the previous answer.

That's it! Your Nginx server should now be configured to use SSL certificates from Let's Encrypt for multiple virtual hosts, and the certificates will be automatically renewed when they expire.
