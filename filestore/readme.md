# Setup filestore to save product thumbnails in a separate directory

## Download OCA repositories including:
* connector   
* queue
* rest-framework
* sale-workflow
* search-engine
* server-env
* server-tools
* shopinvader
* sale-channel
* web-api
* reporting-engine
* server-auth
* product-attribute
* odoo-shopinvader-carrier
* delivery-carrier
* odoo-shopinvader-payment
* sale-promotion
* brand
* storage
* e-commerce

## Download shopinvader repository from ACSONE:
* shopinvader

## Setting up configurations:
1. Create ``/odoo_media/fs_product_image/images`` directory path and set the owner/group to the owner/group of odoo app.
2. Enable ``rwx`` permissions for owner/group, and ``r--`` permissions for others so public people can see and download the thumbnails in the frontend app.
3. Install model ``fs_storage`` which is responsilbe for configuring the filestore for certain models such as ``se.thumbnail``.
4. Enable developer mode and open ``Settings`` ==> ``Technical`` ==> ``FS Storage``.
5. Create a new record for ``fs.storage`` model and assign fields the suitable values:
    + ``Name:`` ``FS Product Image Backend``
    + ``Code:`` ``file``
    + ``Protocol:`` ``file``
    + ``Directory Path:`` ``/odoo_media/fs_product_image/images``
    + ``Model:`` ``se.thumbnail``
    + ``Optimizes Directory Path:`` ``True``
    + ``Base Url:`` ``https://localhost/images/``
    + ``Use X-Sendfile To Serve Internal Url:`` ``True``
    + ``Check Connection Method:`` ``Create Marker file``
6. In NGINX configuration file make sure to create an alias for the filestore directory like this:
    ```NGINX
    server {
            listen 443 ssl;
            server_name localhost;
            proxy_read_timeout 720s;
            proxy_connect_timeout 720s;
            proxy_send_timeout 720s;

            location /images/ {
                    alias /odoo_media/fs_product_image/images/;
                    autoindex off;
                    types {
                            image/jpeg jpeg jpg;
                            image/png png;
                            image/gif gif;
                            image/webp webp;
                    }
                    default_type application/octet-stream;
                    try_files $uri $uri/ =404;
            }
    }
    ```
    In this case the filestore is accessed through this url ``https://localhost/images/``, 
    which refers to that directory ``/odoo_media/fs_product_image/images/``.

    If you click on ``Test Connection`` button it will create an empty file in ``/odoo_media/fs_product_image/images/`` directory.