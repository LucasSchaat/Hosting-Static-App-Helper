# Hosting Cheat Sheet

This repo is intended to be used for quick project deployment and references the following resources

## Resources

- https://github.com/LucasSchaat/Hosting-Digital-Ocean
- https://devmountain.github.io/Host-Helper/
- https://www.robinwieruch.de/deploy-applications-digital-ocean
- https://github.com/gatsbyjs/gatsby/issues/5484

## Steps for hosting static webpages

1. Connect to `ssh` server by typing `ssh root@ip.address.here`
2. Run `npm node build` if using node modules or packages in the application
    - Follow the steps in [this](https://github.com/LucasSchaat/Hosting-Digital-Ocean) repo for setting up the build process
3. Clone repo onto server `git clone GITHUB.URL`
4. Get the build folder or static files folder and copy it in the html folder located at `var/www/domain_name.domain/html`
    - There is a way to try to link the two, but it is tricky to accomplish and may be easier to just copy the file directly into the html folder
    - The caveat here is that any updates to the file that are made via a pull request from GitHub will need to be copied back into this folder ofr those changes to be reflected
5. Setup nginx hosting file using [this](https://devmountain.github.io/Host-Helper/) repo
    - If it is a static file that doesn't require a backend, then you will need to navigate to the nginx directory `etc/nginx/sites-available` and then using the nano editor make changes to the PROJECT_NAME file `nano PROJECT_NAME`
    - The updated nginx file should look similar to the code included below for these static uploads
    
    ```
    server {

        root /var/www/domain_name.domain/html/public;
        index index.html index.htm index.nginx-debian.html;
        # You can host multiple static applications by saving all static
        # files and build folders in the html directory here, but will need
        # to use different folder conventions for directing domains to each
        # specific set of files

        server_name domain_name.domain www.domain_name.domain;

        location / {
            # First attempt to serve request as file, then as directory, then
            # fall back to displaying a 404.
            try_files $uri $uri/ =404;
        }

        listen 443 ssl; # managed by Certbot (optional)

    }

    server {
        if ($host = domain_name.domain) {
            return 301 https://$host$request_uri;
        } # managed by Certbot
        
        if ($host = www.domain_name.domain) {
            return 301 https://$host$request_uri;
        } # managed by Certbot

        listen 80;
        listen [::]:80;

        server_name domain_name.domain www.domain_name.domain;
        return 404; # managed by Certbot
    }
    ```
5.  After this, restart the nginx servers and pm2 servers using the following code prompts in your terminal
    
    ```
    sudo nginx -t
    sudo systemctl restart nginx
    pm2 restart all
    ``` 
6. Now navigate to the registered domain location and check to see if it worked