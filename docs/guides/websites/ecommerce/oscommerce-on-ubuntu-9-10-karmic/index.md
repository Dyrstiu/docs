---
slug: oscommerce-on-ubuntu-9-10-karmic
deprecated: true
author:
  name: Stan Schwertly
  email: docs@linode.com
description: 'Deploying osCommerce, a popular e-commerce web application, on Ubuntu 9.10 (Karmic).'
keywords: ["oscommerce", "ecommerce", "store", "cart", "shop", "shopping"]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
aliases: ['/web-applications/e-commerce/oscommerce/ubuntu-9-10-karmic/','/websites/ecommerce/oscommerce-on-ubuntu-9-10-karmic/']
modified: 2011-08-22
modified_by:
  name: Linode
published: 2010-02-08
title: 'osCommerce on Ubuntu 9.10 (Karmic)'
relations:
    platform:
        key: how-to-install-osCommerce
        keywords:
           - distribution: Ubuntu 9.10
---

osCommerce is an open source solution for creating your own online store. It runs on a LAMP stack and is a strong alternative to Magento, which can be difficult to administer for some.

Before installing osCommerce we assume that you have followed our [Setting Up and Securing a Compute Instance](/docs/guides/set-up-and-secure/). If you are new to Linux server administration, you may be interested in our [introduction to Linux concepts guide](/docs/guides/introduction-to-linux-concepts/), [beginner's guide](/docs/guides/linode-beginners-guide/) and [administration basics guide](/docs/guides/linux-system-administration-basics/). Additionally, osCommerce requires Apache, MySQL, and PHP to be installed. We assume you've followed our [Ubuntu LAMP guide](/docs/guides/lamp-server-on-ubuntu-9-10-karmic/).

## Installation

### Prerequisites

Before installing osCommerce, we must ensure that the `universe` repositories are enabled on your system. Your `/etc/apt/sources.list` should resemble the following (you may have to uncomment or add the `universe` lines:)

{{< file "/etc/apt/sources.list" >}}
## main & restricted repositories
deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

deb http://security.ubuntu.com/ubuntu karmic-security main restricted
deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted

## universe repositories
deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

deb http://security.ubuntu.com/ubuntu karmic-security universe
deb-src http://security.ubuntu.com/ubuntu karmic-security universe

{{< /file >}}


If you had to enable new repositories, issue the following command to update your package lists:

    apt-get update
    apt-get upgrade

Before we begin installing osCommerce, we'll need to install some additional PHP packages as well as the `unzip` tool. Run the following commands:

    apt-get install unzip php5-gd php5-curl
    /etc/init.d/apache2 restart

Installing osCommerce is straightforward and simple. `cd` into your document root directory and download the latest version of osCommerce. You can find the latest version available on [the osCommerce website](http://www.oscommerce.com/solutions/downloads). Run the following commands to install osCommerce in the document root of your website:

    cd /srv/www/example.com/public_html
    wget http://www.oscommerce.com/ext/oscommerce-2.2rc2a.zip
    unzip oscommerce-2.2rc2a.zip
    mv oscommerce-2.2rc2a/catalog/* .

If you want to install osCommerce in a folder, simply `cd` into that folder before downloading and unzipping the package. Now we'll create a database for osCommerce, as well as a user for the new database:

    mysql -u root -p
    CREATE DATABASE oscommerce;
    CREATE USER oscom;
    GRANT ALL ON oscommerce.* TO 'oscom' IDENTIFIED BY 'password';
    exit;

Lastly, change the permissions on the following two `configure.php` files to allow the online setup process to work:

    chmod 777 admin/includes/configure.php includes/configure.php

### Web Configuration

At this point, you can finish the rest of the installation process through the web. Point your browser to the domain or IP of the osCommerce install and append `/install/` to the end. In our example the URL would be `http://www.example.com/install/`. You'll be prompted to fill in your database details. Use "localhost" for the address of the database server, and the credentials for the user and database we created above. The rest of the installation process is self explanatory. After the installation you'll be able to see your store as well as the administrative interface.

## Post Installation

After the installation, certain files need to be removed or renamed for security reasons. Be sure to substitute to correct paths for your particular configuration. First, we need to remove the installation folder:

    rm -rf /srv/www/example.com/public_html/install

Change the permissions on `configure.php` to prevent security issues:

    chmod 644 /srv/www/example.com/public_html/includes/configure.php

Change the permissions of the `images` and `graphs` directory to be accessible by the server:

    chmod -R 777 /srv/www/example.com/public_html/images/
    chmod -R 777 /srv/www/example.com/public_html/admin/images/graphs

Finally, change the permissions of the `backups` directory to be accessible by the server:

    chmod -R 777 /srv/www/example.com/public_html/admin/backups

From here you can begin customizing your store. The default index page will give you instructions for where to begin. You can also check our "More Information" section below.

## SSL Certificates

You may want to install a commercial SSL certificate on your store to encrypt the data sent from your customer to your server. After [Obtaining a Commercial SSL Certificate](/docs/guides/obtain-a-commercially-signed-tls-certificate/), you'll need to make a couple of changes to your `includes/configure.php` file. Below is an example section from that file that highlights the changes you need to make:

{{< file "/srv/www/example.com/public\\_html/includes/configure.php" php >}}
// Define the webserver and path parameters
// * DIR_FS_* = Filesystem directories (local/physical)
// * DIR_WS_* = Webserver directories (virtual/URL)
define('HTTP_SERVER', 'http://www.example.com'); // eg, http://localhost - should not be empty for productive servers
define('HTTPS_SERVER', 'https://example.com'); // eg, https://localhost - should not be empty for productive servers
define('ENABLE_SSL', true); // secure webserver for checkout procedure?
define('HTTP_COOKIE_DOMAIN', 'www.example.com');
define('HTTPS_COOKIE_DOMAIN', 'example.com);
define('HTTP_COOKIE_PATH', '/');
define('HTTPS_COOKIE_PATH', '/');
define('DIR_WS_HTTP_CATALOG', '/');
define('DIR_WS_HTTPS_CATALOG', '/');

{{< /file >}}


It should be noted that in this example, the certificate was issued without the `www` qualifier. Your specific requirements may require tweaking.

## Monitor for Software Updates and Security Notices

When running software compiled or installed directly from sources provided by upstream developers, you are responsible for monitoring updates, bug fixes, and security issues. After becoming aware of releases and potential issues, update your software to resolve flaws and prevent possible system compromise. Monitoring releases and maintaining up to date versions of all software is crucial for the security and integrity of a system.

Please monitor the osCommerce security forums and mailing lists to ensure that you are aware of all updates to the software and can upgrade appropriately or apply patches and recompile as needed:

-   [osCommerce Security Forums](http://forums.oscommerce.com/forum/77-security/?s=82fef4edc6197910baa8281a3f82de1b)
-   [osCommerce Mailing Lists](http://oscommerce.list-manage.com/subscribe?u=a5961750a3635e18fdf4bb539&id=10af90c126)

When upstream sources offer new releases, repeat the instructions for installing the osCommerce software as needed. These practices are crucial for the ongoing security and functioning of your system

## More Information

You may wish to consult the following resources for additional information on this topic. While these are provided in the hope that they will be useful, please note that we cannot vouch for the accuracy or timeliness of externally hosted materials.

- [osCommerce Website](http://www.oscommerce.com/)
- [osCommerce Knowledge Base](http://www.oscommerce.info/)
- [osCommerce Forums](http://forums.oscommerce.com/)



