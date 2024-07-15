# gridpane-wordpress-template-all-plugins...

This repo is an example of a repo configured for full deployment (all wp core files, plugins and themes managed by git repo), with all of the requisite plugins required for the following GridPane panel functionality:
- Nginx Page Caching
- Redis Object Caching
- WPFail2Ban Integration
- SendGrid SMTP Integration
- Additional Security Features
  - Disable Emoji
  - Disable RSS
  - Disable Username Enumeration
  - Block WP Scan Agent
  - Block WP Version
- SSO WP Login

## .gpconfig directory

### Deployment Type

As no `.gpconfig/hybrid` token file exists within this repo, it will be deployed as `GridPane Full Git` type deployment.

This means that the repository will be expected to contain all:
- Core
- Plugins
- Themes
  For your WordPress site.

WordPress Core, Plugins and Themes will be owned by root and the system php user will not be able to write to these files.
In addition we will be enabling the `define('DISALLOW_FILE_MODS',true);` constant to disallow file edits.

All updates will need to be done via Git.

### Deployment Scripts

You will find the following empty scripts:
```shell
.gpconfig/predeploy-server.sh
.gpconfig/predeploy.sh
.gpconfig/postdeploy.sh
.gpconfig/postdeploy-server.sh
```

These scripts will execute in the above order.

The `-server.sh` scripts are run as root, with the others running as the site system user.

The scripts are run from the `/.gpconfig` directory.

The root scripts can be useful for running scripts across the server and microservices which need elevated privileges, be careful.

The system user scripts can be useful for running system user functionality, php, wp-cli etc.

Remember to add the correct path to wp core files for wp-cli commands, in the case of a full deploy this would be the directory above.

### Release Directory Retention

```
.gpconfig/keep.releases
```

This repo contains this file, the contents of the file can be an `integer` only, and the integer must be greater than or equal to `3` - this represents the minimum number of releases we will allow you to retain.

This `integer` is the number of releases that the system will retain in the following directory:
```
/var/www/{site.domain}/releases
```

Please see below for details of the releases and release directory naming convention.


## Plugins

Relevant to the GridPane, this repo contains:
```shell
wp-content/plugins/gridpane-redis-object-cache
wp-content/plugins/nginx-helper
wp-content/plugins/wp-fail2ban
wp-content/object-cache.php     <-- drop in from gridpane-redis-object-cache
```

These would be required based on the sites GridPane App configuration of:
- Nginx Page Caching active
- Redis Object Caching active
- WP Fail2Ban integration active

#### Plugin links

If you are using the Nginx Stack and Nginx Page Caching and Redis Object Caching you will need to use these plugins:

[Nginx-Helper (WordPress.org download link)](https://downloads.wordpress.org/plugin/litespeed-cache.4.6.zip)

[GridPane Redis Object Cache (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-redis-object-cache)

If you are using Nginx FastCGI caching, you will also need the GridPane Nginx-Helper Helper. This is not needed for Redis based Page Caching.

[GridPane Nginx Cache Purger (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-nginx-cache-purger)

If, on the other hand, you are using the OLS stack, you will need to configure with the LSCache plugin (for both Page and Redis Object Caching)

[LiteSpeed Cache (WordPress.org download link)](https://downloads.wordpress.org/plugin/litespeed-cache.4.6.zip)

If your site is using the WPFail2Ban integration in the Panel, then you will need this plugin too. On Deploy we detect if this plugin exists and if your site is convigured for it, and look after the symlinking to MU.

[WP Fail2Ban (WordPress.org download link)](https://downloads.wordpress.org/plugin/wp-fail2ban.4.4.0.4.zip)

## MU-Plugins

Relevant to the GridPane, this repo contains:
```shell
wp-content/mu-plugins/gridpane-block-username-enumeration
wp-content/mu-plugins/gridpane-block-wpscan-agent
wp-content/mu-plugins/gridpane-disable-rss
wp-content/mu-plugins/gridpane-kill-all-emoji
wp-content/mu-plugins/gridpane-mailer
wp-content/mu-plugins/gridpane-nginx-cache-purger
wp-content/mu-plugins/gridpane-remove-wp-version
wp-content/mu-plugins/mu-gridpane-block-username-enumeration.php
wp-content/mu-plugins/mu-gridpane-block-wpscan-agent.php
wp-content/mu-plugins/mu-gridpane-disable-rss.php
wp-content/mu-plugins/mu-gridpane-kill-all-emoji.php
wp-content/mu-plugins/mu-gridpane-mailer.php
wp-content/mu-plugins/mu-gridpane-nginx-cache-purger.php
wp-content/mu-plugins/mu-gridpane-remove-wp-version.php
wp-content/mu-plugins/wp-cli-login-server.php
```

These would be required based on the sites GridPane App configuration of:
- Additional Security Settings
  - Disable Emoji active
  - Disable RSS active
  - Disable Username Enumeration active
  - Block WP Scan Agent active
  - Block WP Version active
- Sendgrid SMTP integration active 
- SSO login active

#### MU Plugin links

If your site is using the GridPane Sendgrid Mailer integration, then you will need to include that plugin as an MU-Plugin. The repo linked here has instructions, but you can check the mu-plugins directory of this repo to confirm correct configuration. 

[GridPane Mailer (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-mailer)

If your site is using any of the GridPane Additional Security Beta settings, then you will need the appropriate MU-Plugin for that feature. The repos linked here have instructions, but you can check the mu-plugins directory of this repo to conform correct configuration.

[GridPane Block Username Enumeration (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-block-username-enumeration)  
[GridPane Block-WPScan-Agent (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-block-wpscan-agent)  
[GridPane Disable RSS (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-disable-rss)  
[GridPane Disable Emoji / Kill All Emoji (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-kill-all-emoji)  
[GridPane Remove WP Version (GridPane-Dev GitHub Page)](https://github.com/GridPane-Dev/gridpane-remove-wp-version)

All sites require the wp-cli-login-server.php mu plugin for SSO to function. If this is not found we will install it on SSO if possible, but it might make sense to just include in your repo and control it's update too.

[WP-CLI login server (aaemnnosttv Github)](https://github.com/aaemnnosttv/wp-cli-login-server/blob/master/wp-cli-login-server.php)

After deploy, a function runs and checks for all of the above and will sync the state to the app with the site set as the source of truth. If anything is missing (apart from the login server)
we will just adjust the app data to match.

## Themes

This repo is configured with all base core themes from `twentyten` to `twentytwentytwo` 

```shell
wp-content/themes/twentyeleven
wp-content/themes/twentyfifteen
wp-content/themes/twentyfourteen
wp-content/themes/twentynineteen
wp-content/themes/twentyseventeen
wp-content/themes/twentysixteen
wp-content/themes/twentyten
wp-content/themes/twentythirteen
wp-content/themes/twentytwelve
wp-content/themes/twentytwenty
wp-content/themes/twentytwentyone
wp-content/themes/twentytwentytwo
```

## Directory Structure Changes

### Directory Structure when Git Integration is enabled

When you first enable the Git Integration Connection for a site, we run a function that modifies the Site's directory file structure.
```
root@10alpha:/var/www/test.site# ls -la
total 52
drwxr-xr-x+ 9 test10350 test10350 4096 Jun 29 13:03 .
drwxr-xr-x+ 6 root      root      4096 Jun 29 13:02 ..
drwxr--r--  3 root      root      4096 Jun 29 13:02 .duplicacy
-rw-r--r--  1 test10350 test10350  281 Jun 29 13:02 .user.ini
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:02 dns
lrwxrwxrwx  1 root      root        46 Jun 29 13:03 htdocs -> /var/www/test.site/releases/release-1656507770
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:03 logs
drwxr-xr-x  3 test10350 test10350 4096 Jun 29 13:02 modsec
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:02 nginx
drwxr-xr-x  4 test10350 test10350 4096 Jun 29 13:02 public
drwxr-xr-x  3 test10350 test10350 4096 Jun 29 13:02 releases
-rw-r--r--  1 test10350 test10350  120 Jun 29 13:02 user-configs.php
-rw-r--r--  1 test10350 test10350 4189 Jun 29 13:02 wp-config.php
```

A `/var/www/{site.domain}/releases` diretory is created, and within that a release `/var/www/{site.domain}/release-{EPOCH}` subdirectory.

The site's files and directories are moved into the release directory, and then `htdocs` is symlinked to the release directory.

The site `wp-config.php` file is symlinked into the `releases` directory so that is available.
```
root@10alpha:/var/www/test.site# ls -la releases
total 20
drwxr-xr-x  5 test10350 test10350 4096 Jun 29 13:16 .
drwxr-xr-x+ 9 test10350 test10350 4096 Jun 29 13:16 ..
drwxr-xr-x  5 test10350 test10350 4096 Jun 29 13:03 release-1656507770
lrwxrwxrwx  1 root      root        32 Jun 29 13:16 wp-config.php -> /var/www/test.site/wp-config.php
```

You may have noticed the `.user.ini` file is now located in `/var/www/{site.domain}/.user.ini`, that is because it is now symlinked into the current release:
```
root@10alpha:/var/www/test.site# ls -la htdocs/.user.ini
lrwxrwxrwx 1 root root 28 Jun 29 13:03 htdocs/.user.ini -> /var/www/test.site/.user.ini
```
or
```
root@10alpha:/var/www/test.site# ls -la /var/www/test.site/releases/release-1656507770/.user.ini
lrwxrwxrwx 1 root root 28 Jun 29 13:03 /var/www/test.site/releases/release-1656507770/.user.ini -> /var/www/test.site/.user.ini
```
(If you are using OLS stack, this is also the case for the `.htaccess` file)

You will have also noticed the `/var/www/{site.domain}/public` directory. This directory is now for all public files, ie your uploads directory is symlinked here.
Files in this directory are not version controlled and they are not immutable.
```
root@10alpha:/var/www/test.site# ls -la htdocs/wp-content/uploads
lrwxrwxrwx 1 root root 25 Jun 29 13:03 htdocs/wp-content/uploads -> /var/www/test.site/public
```

At this point:
- The site's directory structure has been adapted for Git deployments, but the all files in all directories are the original files, your Git repo has not yet been deployed.
- The structure is also not immutable, all the files are owned by the system user, and file modifications are still allowed.

```
root@10alpha:/var/www/test.site# ls -la /var/www/test.site/releases/release-1656507770
total 220
drwxr-xr-x  5 test10350 test10350  4096 Jun 29 13:03 .
drwxr-xr-x  7 test10350 test10350  4096 Jun 29 13:28 ..
lrwxrwxrwx  1 root      root         28 Jun 29 13:03 .user.ini -> /var/www/test.site/.user.ini
-rw-r--r--  1 test10350 test10350   405 Jun 29 13:02 index.php
-rw-r--r--  1 test10350 test10350 19915 Jun 29 13:02 license.txt
-rw-r--r--  1 test10350 test10350  7401 Jun 29 13:02 readme.html
-rw-r--r--  1 test10350 test10350  7165 Jun 29 13:02 wp-activate.php
drwxr-xr-x  9 test10350 test10350  4096 Jun 29 13:02 wp-admin
-rw-r--r--  1 test10350 test10350   351 Jun 29 13:02 wp-blog-header.php
-rw-r--r--  1 test10350 test10350  2338 Jun 29 13:02 wp-comments-post.php
-rw-r--r--  1 test10350 test10350  3001 Jun 29 13:02 wp-config-sample.php
drwxr-xr-x  6 test10350 test10350  4096 Jun 29 13:03 wp-content
-rw-r--r--  1 test10350 test10350  3943 Jun 29 13:02 wp-cron.php
drwxr-xr-x 26 test10350 test10350 12288 Jun 29 13:02 wp-includes
-rw-r--r--  1 test10350 test10350  2494 Jun 29 13:02 wp-links-opml.php
-rw-r--r--  1 test10350 test10350  3973 Jun 29 13:02 wp-load.php
-rw-r--r--  1 test10350 test10350 48498 Jun 29 13:02 wp-login.php
-rw-r--r--  1 test10350 test10350  8577 Jun 29 13:02 wp-mail.php
-rw-r--r--  1 test10350 test10350 23706 Jun 29 13:02 wp-settings.php
-rw-r--r--  1 test10350 test10350 32051 Jun 29 13:02 wp-signup.php
-rw-r--r--  1 test10350 test10350  4748 Jun 29 13:02 wp-trackback.php
-rw-r--r--  1 test10350 test10350  3236 Jun 29 13:02 xmlrpc.php
```

### Directory Structure when Git Repo is deployed

Once you click Deploy, or if you have push to deploy enabled and push an update, a deploy will be triggered on your server.

After the deploy finishes, your git repo will have been pulled into a new directory, scripts will have run and if all passed, then the directory symlinks will have been updated.
However the basic directory structure will be as above.
```
root@10alpha:/var/www/test.site# ls -la
drwxr-xr-x+ 9 test10350 test10350 4096 Jun 29 13:31 .
drwxr-xr-x+ 6 root      root      4096 Jun 29 13:02 ..
drwxr--r--  3 root      root      4096 Jun 29 13:02 .duplicacy
-rw-r--r--  1 test10350 test10350  281 Jun 29 13:02 .user.ini
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:02 dns
lrwxrwxrwx  1 root      root        46 Jun 29 13:31 htdocs -> /var/www/test.site/releases/release-1656509495
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:31 logs
drwxr-xr-x  3 test10350 test10350 4096 Jun 29 13:02 modsec
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:02 nginx
drwxr-xr-x  4 test10350 test10350 4096 Jun 29 13:02 public
drwxr-xr-x  8 test10350 test10350 4096 Jun 29 13:31 releases
-rw-r--r--  1 test10350 test10350  120 Jun 29 13:02 user-configs.php
-rw-r--r--  1 test10350 test10350 4321 Jun 29 13:31 wp-config.php
```

But now the site WordPress files and directories will be root owned:
```
root@10alpha:/var/www/test.site# ls -la /var/www/test.site/releases/release-1656509495
total 232
drwxr-xr-x  7 root      root       4096 Jun 29 13:31 .
drwxr-xr-x  8 test10350 test10350  4096 Jun 29 13:31 ..
drwxr-xr-x  8 root      root       4096 Jun 29 13:31 .git
drwxr-xr-x  3 root      root       4096 Jun 29 13:31 .github
-rw-r--r--  1 root      root         30 Jun 29 13:31 README.md
-rw-r--r--  1 root      root        405 Jun 29 13:31 index.php
-rw-r--r--  1 root      root      19915 Jun 29 13:31 license.txt
-rw-r--r--  1 root      root       7401 Jun 29 13:31 readme.html
-rw-r--r--  1 root      root       7165 Jun 29 13:31 wp-activate.php
drwxr-xr-x  9 root      root       4096 Jun 29 13:31 wp-admin
-rw-r--r--  1 root      root        351 Jun 29 13:31 wp-blog-header.php
-rw-r--r--  1 root      root       2338 Jun 29 13:31 wp-comments-post.php
-rw-r--r--  1 root      root       3001 Jun 29 13:31 wp-config-sample.php
drwxr-xr-x  4 root      root       4096 Jun 29 13:31 wp-content
-rw-r--r--  1 root      root       3943 Jun 29 13:31 wp-cron.php
drwxr-xr-x 26 root      root      12288 Jun 29 13:31 wp-includes
-rw-r--r--  1 root      root       2494 Jun 29 13:31 wp-links-opml.php
-rw-r--r--  1 root      root       3973 Jun 29 13:31 wp-load.php
-rw-r--r--  1 root      root      48498 Jun 29 13:31 wp-login.php
-rw-r--r--  1 root      root       8577 Jun 29 13:31 wp-mail.php
-rw-r--r--  1 root      root      23706 Jun 29 13:31 wp-settings.php
-rw-r--r--  1 root      root      32051 Jun 29 13:31 wp-signup.php
-rw-r--r--  1 root      root       4748 Jun 29 13:31 wp-trackback.php
-rw-r--r--  1 root      root       3236 Jun 29 13:31 xmlrpc.php
```
```
root@10alpha:/var/www/test.site/htdocs/wp-content# ls -la
total 20
drwxr-xr-x  4 root root 4096 Jun 29 13:31 .
drwxr-xr-x  7 root root 4096 Jun 29 13:31 ..
-rw-r--r--  1 root root   28 Jun 29 13:31 index.php
drwxr-xr-x  2 root root 4096 Jun 29 13:31 plugins
drwxr-xr-x 14 root root 4096 Jun 29 13:31 themes
lrwxrwxrwx  1 root root   25 Jun 29 13:31 uploads -> /var/www/test.site/public
```
Apart from the contents of your uploads directory, which will still be owned by the site system user:
```
root@10alpha:/var/www/test.site/htdocs/wp-content/uploads# ls -la
total 16
drwxr-xr-x  4 test10350 test10350 4096 Jun 29 13:02 .
drwxr-xr-x+ 9 test10350 test10350 4096 Jun 29 13:31 ..
drwxr-xr-x  3 test10350 test10350 4096 Jun 29 13:02 2022
drwxr-xr-x  2 test10350 test10350 4096 Jun 29 13:02 nginx-helper
```

Your site `/var/www/{site.domain}/wp-config.php` will also have been updated to include:
```
/* GridPane Git Full Immutable Start */
define('DISALLOW_FILE_MODS', true);
/* GridPane Git Full Immutable End */
```
To further restrict any file modifications from within the app.

If you disable the git integration for your site, then the directory and file structure will be returned to GridPane normal using the latest files from the current release.

# Notes

Directory structures - intermediary state
- On enable we currently check the repo and then configure a sites directories for git deployments, but do not carry out an immediate deployment.
- First deployment occurs on the push of the deploy button or trigger of the push to deploy webhook.
- This means after enable and before deploy there is an intermediary directory state where the structure is that of a git deployed site, but the files are not from the git repo.
- We understand this is superfluous and intend to combine this into a single action, however at beta stage it allows us to test and check each stage separately.

Backups 
- restores should be limited to db only
- the restore archives will still contain a copy of the files if needed.

Cloning 
- source site is git full deployed
  - cloning should work as normal

Clone Over
- source site is git full deployed 
  - clone over should work as normal
- destination site is git full deployed 
  - clone over should only allow db clones

Staging
- source site is git full deployed 
  - staging should work fine, the destination site should be functional but without any traces of git
- destination site is git full deployed
  - staging should be limited to only db pushes 

Panel Toggle Features
- Currently if deploys include correct plugins to match the site panel toggles then the sync make sure toggles are right
- But using toggles on panel to update site when configured for git full immutable is unpredictable
  - our mu plugins install because we do it via root and our own files on server - security and mailing
    - fix incoming - we will refuse to enable/disable unless deployed/removed via git
    - informative notifications to direct users to deploy with/without plugins to enable/disable automatically
  - wpfail2ban fails to install but the wp-config is configured and the toggles turn on
    - fix incoming - we will refuse to enable/disable unless deployed/removed via git with correct notifications/state
    - Once deployed via git, toggles will become functinoal for features
    - informative notifications to direct users to redeploy with plugin then toggle
  - redis object caching won't enable because it can't install the plugin
    - fix incoming - we will refuse to enable/disable by the panel unless deployed via git
    - will accept gridpane plugin or till kruss plugin
    - informative notifications to direct users to redeploy with plugin then toggle
  - nginx caching enables - but the nginx-helper plugin wont install - so no cache clearance
    - Caching toggle will stay on as server caching is enabled 
    - informative warning notifications to let user know to redeploy with plugin for clearance function
  - LS Caching enable - lscache plugin won't install
    - Fix incoming - caching will not enable unless the plugin has been included via repo
    - informative notification to let user know to redploy then toggle









