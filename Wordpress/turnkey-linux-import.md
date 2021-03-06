# Turnkey Linux for Wordpress #

This guide is a quick-and-dirty setup for importing an existing Wordpress site into a virtual machine environment for testing new features.  This assumes you have access to a full backup of the Wordpress site in questions (refered to here as <code>backedupsite</code>.

## Get Turnkey Linux VM for Wordpress ##

You can use VMware Player on Windows or Fusion on Mac.  Be sure to set bridged networking so you can access the VM from another computer.

	* http://www.turnkeylinux.org/wordpress

## After VM installation and setup ##

The goal here is to import our backed-up Wordpress database into the turnkey instance.

	* Access ‘phpadmin’ web interface at https://192.168.1.240:12322
		* Webmin, SSH, MySQL, phpMyAdmin: username root
		* Wordpress: username admin
	* Navigate to the ‘wordpress’ database using the GUI
	* Import ‘wordpress_0.sql’ file from the backup tarball into the ‘wordpress’ database.

## Operations to perform as ‘root’ ##

The goal here is to re-use the turnkey Wordpress configuration after restoring our backed-up site.

	# /etc/init.d/apache2 stop
	# /etc/init.d/mysql stop
	# cd /var/www
	# mv wordpress wordpress.orig
	# mv ~/backedupsite wordpress
	# chown -R www-data:www-data wordpress
	# cp /var/www/wordpress/wp-config.php /var/www/wordpress/wp-config.php.backup
	# cp /var/www/wordpress.orig/wp-config.php /var/www/wordpress/wp-config.php
	# /etc/init.d/mysql start
	# /etc/init.d/apache2 start
	# vi /etc/php5/*/php.ini (set upload_max_filesize to 64M)

## Install the wp-cli utility ##

The wp-cli utility can be found at http://wp-cli.org/.  Install as 'root' using:

	# curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
	# chmod +x wp-cli.phar
	# sudo mv wp-cli.phar /usr/local/bin/wp

Now, update the imported wordpress site, if desired, using wp-cli commands:

	# su - nobody
	# cd /var/www/wordpress
	# wp themes update --all
	# wp plugins update --all

## Modify your theme's functions.php to make links relative ##

```php
  add_action( 'template_redirect', 'rw_relative_urls' );

  function rw_relative_urls() {
    // Don't do anything if:
    // - In feed
    // - In sitemap by WordPress SEO plugin
    if ( is_feed() || get_query_var( 'sitemap' ) ) {
        return;
    }

    $filters = array(
        'post_link',
        'post_type_link',
        'page_link',
        'attachment_link',
        'get_shortlink',
        'post_type_archive_link',
        'get_pagenum_link',
        'get_comments_pagenum_link',
        'term_link',
        'search_link',
        'day_link',
        'month_link',
        'year_link'
    );
    
    foreach ( $filters as $filter ) {
        add_filter( $filter, 'wp_make_link_relative' );
    }
  }
```
