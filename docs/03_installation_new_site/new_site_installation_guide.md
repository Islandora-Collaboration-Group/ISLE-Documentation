# Build a New ISLE / Islandora Environment

_Expectations:  It takes an minimum of **4 - 6 hours or more** to read this entire document and perform the installation as prescribed._

This guide documents how an enduser can spin up and install the ISLE / Islandora environment tailored to using their own domain / URL.

This new ISLE / Islandora environment can include the option to create an un-themed Drupal website and empty Fedora repository for endusers to develop code, perform ingests, edit metadata, update fields in SOLR indexing all essential in ultimately creating a new ISLE production site.

While this checklist will attempt to point out most of the usage challenges or pitfalls, ISLE assumes no responsibility or liability in this matter should an enduser have customizations beyond what this guide outlines.

**Please note:** There is a [Glossary](../../glossary) with relevant terms to help guide installation.

### Assumptions / Prerequisites

* Comfortability with ISLE. Recommend first setting up the ISLE Test Site (isle.localdomain) via the [Install Start Here](../../install_start_here) guide. If you have already done this, please proceed.

* Host Server that conforms to the specifications outlined in the [Host Server Specifications](../../01_installation_host_server/host_server_system_specifications/)

* This new site guide is designed for a Host server that has already followed the appropriate setup and configuration instructions in the `Create a new ISLE site` section of the [Install Start Here](../install_start_here.md) guide.

* Instructions below also assume a MacOS or Linux laptop or workstation to be used in conjunction with the ISLE Host Server for deploying configs, code, files etc. Windows users may have to adjust / swap out various tools as needed.

* These directions also depend on the type of local computer used to connect via browser to Islandora.

* Have an existing domain name that works - is set up with DNS etc...

* Have SSL Certificates for the domain. (_Please work with the appropriate internal IT resource to provision these files for your domain_)

* ISLE project has been cloned to BOTH your local laptop/workstation AND the ISLE host server


## Overview

* Setup a Private Code Repository
      * Most of the work in this guide involves careful editing of the various configuration and settings files that customize the pieces of Islandora (database, repository, web-server, etc...).
      * Doing this work in a code repository makes it easier to correct errors and to repeat the process for additional servers without needing to replicate all the work.
      * Since the edits could include things like passwords, it's important to make this a private repository.

* Customizing for your Environment
      * Many of the steps below describe adding the domain name or other specific bits of information into files or appending those bits to file names.
      * In these cases this guide will call out the customization point AND provide an example - it's important not to literally copy paste the example!

## Create Private Code Repository

**ON your local laptop/workstation:**

* Using a git repository of your choice (GitHub, GitLab, Bitbucket, etc.) create a PRIVATE remote git repo - see the specific code repository documentation online for setup instructions.

* Open a terminal - navigate to `/opt/ISLE/config` (_or where you put the ISLE directory on your local workstation._)

* Create a new directory with a name of your choice

   * Example: where "digital-collections.yourdomain.com" is your server domain name e.g.:  `config/digital-collections.example.edu`

   * Please do not use this literal value.

* Locate the directory within the `/opt/ISLE/config/` directory called `isle-newsite-sample` and copy all of its contents into your newly created directory.

* `cd` into the newly copied and renamed `digital-collections.example.edu` directory and type:
 `git init` to imitate this directory as a code repository.

* `git remote add NameOfYourRepository URLofYourRepository` to connect your local repository to the remote you set up in the above steps.

    * [**NOTE** replace "NameOfYourRepository" and "URLofYourRepository" with the name of your git repository and its URL]

* You are now ready to perform the customization edits in this directory (you can use a text editor of choice now don't have to stay in terminal - just locate the folder in the finder and open file in text editor)

##  Edits

### Docker compose file:

* Edit the file: **docker-compose.yml** accordingly:

      * Change container names based on the environment (prod, stage, dev, etc)

      * For example if you are building a dev server instance:

          `container_name: isle-solr-newsite`

          changes to:

          `container_name: isle-solr-dev`

          and so on...

**Please note:** Much of the file is already with comments guiding the enduser to key areas or files to edit or modify accordingly.

---

## Config directories

Now proceed to edits within the child directories, make edits to config files:

a. to reflect **environment** (prod, stage, dev, etc...)

b. to involve your **domain name** (digital-collections.example.edu)

----

### Apache directory

* Copy your SSL certificates for Apache into `apache/ssl/certs`

* Within the `sites-enabled` directory, rename the files newsite-sample-ssl.conf and newsite-sample.conf to your domain names example:

    `digital-collections.example.edu_ssl.conf`
    `digital-collections.example.edu.conf`

* edit the  `yourdomain_ssl.conf` file and change lines 4 and 5 to point to the location of your apache logs - example:

    `ErrorLog /var/log/apache2/digital-collections.example.edu.ssl.error.log`
    `CustomLog /var/log/apache2/digital-collections.example.edu.ssl.access.log combined`


* edit the  `yourdomain_ssl.conf` file and change lines 12 and 13 to point to the location of your certs - example:

        `SSLCertificateFile    /certs/ssl-cert-example.pem`
        `SSLCertificateKeyFile /certs/ssl-cert-example.key`

* edit the file: `settings.php`   lines 251-253 add your database name, database user, and database password    

* edit the file: `settings.php`   line 288 to include a 45+ alpha-numeric characters drupal hash between the quotes after this text: `$drupal_hash_salt = '';`

   * You'll need to create this value, recommend using a password generator tool. Ensure only alpha-numeric characters are used, no symbols etc.

* review the file: `settings.php`  near line 311 to ensure `# $base_url = ` is commented out

* edit the file: `apache_provision.sh`  line 47:

`/usr/local/bin/drush site-install -y --account-name=isle_localdomain_admin --account-pass=isle_localdomain_adminpw2018 --account-mail=admin@isle.localdomain --site-name=“ISLE.localdomain”`

Change the following in this line to the appropriate names and passwords for your site

    account name
    account-pass
    account-mail
    site-name

----
### Fedora directory

* Within the fedora/fedora directories, **change the passwords** in the following files:

          fedora-users.xml  (change all applicable)
          fedora.fsfg  (line numbers see below)
          filter-drupal.xml  (change db name, db user, db psswd)

* within the `gsearch` directory, edit the file: `fedoragsearch.properties` at line 7 add a space after the equal sign and add the fgsAdmin password

` -  fedoragsearch.soapPass                =`

* Edit the file: `fgsconfig-basic-configForIslandora.properties`  line 26: edit the gsearch password

`gsearchPass=ild_fgs_admin_2018`

* in the same file edit line 67 edit the fedora password

`fedoraPass=ild_fed_admin_2018`

* Edit the file: `fedora/gsearch/fgsconfigObjects.properties` line 15: edit the fedora admin password

`fgsconfigObjects.fedoraPass            = ild_fed_admin_2018`

* Edit the file: `fedora/gsearch/repository.properties` line 7: edit the fedora admin password

`fgsrepository.fedoraPass        = ild_fed_admin_2018`

---
### Tomcat directory

* Within the **tomcat** sub directories:

 Strongly recommend changing tomcat user passwords in file: `fedora/tomcat/tomcat-users.xml`

 ONLY CHANGE TOMCAT USERNAME/PASSWORD - looks like this:

          <!-- user manager can access only manager section -->
             <user username=“manager” password=“ild_tc_man_2018" roles=“manager-gui” />  <!--enduser please add passwords / users and remove this comment -->

          <!-- user admin can access manager and admin section both -->
             <user username=“admin” password=“ild_tc_adm_2018” roles=“manager-gui,admin-gui” /> <!--enduser please add passwords / users and remove this comment -->

          </tomcat-users>

----

### Repository policies

Edit the contents of the repository-policies directory as necessary IF YOU NEED TO otherwise leave alone.

------

### Mysql directory

* edit the contents of `create_drupal_user.sql` to create the drupal site db user

* edit the contents of `create_fedora_user.sql` to create the fedora db user

* edit the contents of `fedora3.sql` to create the fedora db

* edit the contents of `site.sql` to create the drupal site db

* make any appropriate edits as you see fit to `my.conf` IF NEEDED

 -------

### Proxy directory

* in the `config/sites-enabled` directory, rename the `conf` file to `yourdomain.conf` (where "yourdomain" is the domain name of your server)
  * for example: `digital-collections.example.edu.conf`

* Now edit the file...

    * change line 6 `server_name` to `yourdomainname` (where "yourdomain" is the domain name of your server)
    * change line 15 `server_name` to `yourdomainname` (where "yourdomain" is the domain name of your server)
    * change lines 18 and 19 to the name of your ssl certs (should be same names as in apache edits above)
    * edit lines 36 and 37 - uncomment: `ssl_stapling on;` and uncomment: `ssl_stapling_verify on;`
    * uncomment line 40:  `add_header Strict-Transport-Security max-age=15768000;`

    * append your environment type (prod, dev, stage, etc.) to the following lines proceeded by a hyphen i.e. `-dev`

----
### SSL-certs directory
Copy your SSL certs into the ssl-certs dir
DO NOT OVERWRITE OR DELETE the dhparam.pem file that's in There
--------

### Proxy upstreams.d directory

* copy and rename: `sample-upstreams.conf.disabled` - replace "sample" in the file name w/ the environment name e.g. (dev, prod, stage, etc...)

* lines 10 and 14 look like this:

        upstream fedora-internal {
          server fedora:8080 fail_timeout=0;
        }

        upstream apache-internal {
          server apache:443 fail_timeout=0;
        }

* Append the environment name after  both instances of "internal" proceeded by a hyphen "-dev" in this example:

          upstream fedora-internal-dev {
          server fedora:8080 fail_timeout=0;
        }

        upstream apache-internal-dev {
          server apache:443 fail_timeout=0;
        }

-----

### Solr directory

* Within the tomcat sub-directories

Strongly rec you change tomcat user passwords in file: `fedora/tomcat/tomcat-users.xml`

**ONLY CHANGE TOMCAT PASSWORD**

looks like this:

      <!-- user manager can access only manager section -->
         <user username=“manager” password=“ild_tc_man_2018" roles=“manager-gui” />  <!--enduser please add passwords / users and remove this comment -->

      <!-- user admin can access manager and admin section both -->
         <user username=“admin” password=“ild_tc_adm_2018” roles=“manager-gui,admin-gui” /> <!--enduser please add passwords / users and remove this comment -->

      </tomcat-users>

--------

## Final steps

* Now that all the changes are made (be sure to save), ISLE should be ready to test. First you'll need to push these changes to your private code repository.

    * Open a terminal - `cd` to the config directory you've been making the changes in...

    * `git status`  this will show you all the files that have been modified and ready to be added to your private repo along with handy paths for the next steps.

    * `git add /pathtoyourmodifiedfile` (replace "pathtoyourmodifiedfile" with the path to your config directory)

    * run these:

    `git add apache`
    `git add fedora`
    `git add mysql`
    `git add proxy`
    `git add solr`
    `git add docker-compose.yml`

    * run `git status` again - everything should be in green now as all modified files have been added - if anything's still red use git add and the path to add it

    * then run `git commit -m "initial config commit"` inside the double quotes is the commit message you can say whatever you want in this message - so for example if this is the config for your dev instance you could say that...

    * run `git push origin master` this will push all your changes to the repo further changes should be made on branches for different servers or to master branch for this same server


--------

## Clone Custom Configuration to Host Server

* Open a terminal - ssh into your host server using the `islandora` user

* `cd` to `/opt/ISLE/config/`

* because your customizations are in a **private** repository, you'll need to add this islandora user's `id_rsa.pub` key to the repository via the website.

* This key is found in `/home/islandora/.ssh/id_rsa.pub`

* One can simply run `cat /home/islandora/.ssh/id_rsa.pub` and copy the output (ensure no whitespaces or extra returns)

* Go to the repository web site with a browser and locate your private repository.

      * locate the ssh key entry location (in Bitbucket this is in Settings / Access Keys /
      * **add** an ssh key w/ the label **islandora host server** paste in the key text and click **Add Key**

* Back at your terminal command line, run `git clone URLpathtoyourremoteprivaterepo.git .` (replacing "URLpathtoyourremoteprivaterepo" with the URL to the repository provided by the website)

* `cd` into the newly cloned directory

## Spin up ISLE containers!

* run `docker-compose up -d`

* run `docker exec yourApacheContainerNameHere bash` (replace 993845754398)
use the name from the docker-composer.yml file here (typical: isle-apache-prod, or -stage, etc...)

cd to   `cd /tmp/isle_drupal_build_tools`

then run:
`./apache_provision.sh`

give this a few minutes and check the site - should be up now!



--------
