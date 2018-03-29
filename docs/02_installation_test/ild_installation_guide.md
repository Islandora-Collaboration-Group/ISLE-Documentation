_Expectations:  It takes an average of **15-45 minutes** to read this entire document and perform the installation as prescribed._

Follow this guide to spin up and install ISLE utilizing the built-in `isle.localdomain` domain name for review and testing.

This test ISLE / Islandora environment (`isle.localdomain`) includes an un-themed Drupal website and empty Fedora repository for endusers to develop code, test ingests, test metadata, update fields in SOLR indexing and otherwise "kick the tires" on ISLE prior for further usages e.g. creating a new ISLE production site or migrating a current Islandora production site to ISLE.

While this checklist will attempt to point out most of the usage challenges or pitfalls, ISLE assumes no responsibility or liability in this matter should an enduser have customizations beyond what this guide outlines.

**Please note:** There is a [Glossary](../../glossary) with relevant terms to help guide installation.

### Assumptions / Prerequisites

* Laptop / workstation that conforms to the specifications outlined in the [Host Server Specifications](../../01_installation_host_server/host_server_system_specifications/)

* This test site guide is designed for a local laptop / workstation that has already followed the appropriate setup and configuration instructions in the `Test ISLE` section of the [Install Start Here](../install_start_here.md) guide.

* Instructions below assume a MacOS or Linux laptop or workstation. Windows users may have to adjust / swap out various tools as needed.

* These directions depend on the type of local computer used to connect via browser to Islandora.

---

### Step 0: edit /etc/hosts on local laptop or desktop

#### Edit 1: Using Vagrant on all OSes

* **Vagrant** If you are using Vagrant on a laptop move on to the next section (_Summary: Test site launch process_) as this part is handled automatically.

---

#### Edit 1: Using Virtualbox VM (non-Vagrant) on MacOS or Ubuntu Desktop

* From the instructions in setting up the Virtualbox VM on your OS (MacOS, Ubuntu or Windows), the IP used to setup the Host-Only network was `10.10.10.130`

* Add the value of `10.10.10.130 isle.localdomain` to the laptop / workstation's `/etc/hosts` file.   

* For endusers running MacOS and Ubuntu Desktop:

   * Open a terminal on the local laptop

   * Enter: `sudo nano /etc/hosts`
     * _For endusers familiar with editing files on the command line, vim or alternative tools can be used in lieu of nano_

   * Enter the laptop enduser password

   * Add the values below the `127.0.0.1` entry in the `/etc/hosts` file.

       * `10.10.10.130 isle.localdomain`  

**Example**

```
  127.0.0.1 localhost
  10.10.10.130 isle.localdomain
```

* Enter `Cntrl` and the letter `o` together to write the changes to the file.
* Enter `Cntrl` and the letter `x` together to exit the file.

---

#### Edit 1: Using Virtualbox VM (non-Vagrant) on Windows

* For endusers running Windows 10:

    * Press the Windows key.

    * Type `Notepad` in the search field.

    * In the search results, right-click `Notepad` and select `Run as administrator`.

    * From `Notepad`, open the following file: `C:\Windows\System32\Drivers\etc\hosts`

    * Add the values below the `127.0.0.1` entry in the `/etc/hosts` file.

        * `10.10.10.130 isle.localdomain`  

    * Click File > Save to save your changes.

---

#### Edit 1: Using Docker for Mac

* **Docker For Mac** If you are using Docker For Mac, then use the IP address of `127.0.0.1`

* Open a terminal on the local laptop

* Enter: `sudo nano /etc/hosts`
  * _For endusers familiar with editing files on the command line, vim or alternative tools can be used in lieu of nano_

* Enter the laptop enduser password

* Add the values next to the `127.0.0.1 localhost` entry in the `/etc/hosts` file.

    * `127.0.0.1 localhost isle.localdomain` with a space in between the entries.  

  * Enter `Cntrl` and the letter `o` together to write the changes to the file.

  * Enter `Cntrl` and the letter `x` together to exit the file.

---

#### Edit 1: Using Docker for Windows

* For endusers running Windows 10:

    * Press the Windows key.

    * Type `Notepad` in the search field.

    * In the search results, right-click `Notepad` and select `Run as administrator`.

    * From `Notepad`, open the following file: `C:\Windows\System32\Drivers\etc\hosts`

    * Add the values next to the `127.0.0.1 localhost` entry in the `/etc/hosts` file.

        * `127.0.0.1 localhost isle.localdomain` with a space in between the entries.

    * Click File > Save to save your changes.

---

### Step 1: Test site launch process

* If using:

    * **Vagrant or a Virtualbox VM:** The steps below assume you are shelled in (connected by SSH) as the `islandora` user into the ISLE Host VM via a terminal on a local laptop.

        * `ssh islandora@10.10.10.130`

        * `cd /opt/ISLE`

    * **Docker for Mac:** Continue to use the open terminal and navigate `cd /opt/ISLE` to the ISLE project directory.

    * **Docker for Windows:** Continue to use the open PowerShell window and navigate to the ISLE project directory if not already there: `cd ~\Documents\ISLE`.

The following steps below are for all users (_Vagrant, VM, non Vagrant and Docker for MacOS, Windows and Ubuntu Desktop users alike._)

The install times stated below for each container are highly dependent on the enduser's available Internet bandwidth and could take more or less time accordingly.

* Within the open terminal, run:

    *  `docker-compose up -d`

* This is going to download and start all five ISLE Docker images (_roughly 6 GB of data so it may take a little while depending on your Internet connection_)

* To check if the containers are running: `docker ps` (shows only running containers)

    * If you don't see all five containers try: `docker ps -a` (shows all containers running or not)

**If all five containers are NOT running, then proceed to the Troubleshooting section first before advancing to the "Install script on Apache container" section below**


-----

#### Step 2: Run install script on Apache container

**Total build process** may take up to 15 - 45 minutes (_depending on system and internet speeds_)

* Run the following shell scripts manually on the apache container  

    * `docker exec -it isle-apache-ld bash`

_For Windows Users only_

* You may be prompted by Windows to:
    * Share the C drive with Docker.  Click okay or allow
        * Enter your username and password
        * Allow vpnkit.exe to communicate with the network.  Click Okay or Allow to accept the default selection.
        * If the process seems to halt, check the taskbar for background windows.

---

* This will put you into a root prompt at the command line where you will enter the following commands:
    * `cd /tmp/isle_drupal_build_tools/`

    * `chmod 777 *.sh`

    * `./install_isle_ld_site.sh`

        * **This script will take some time (see estimate above.)** You should see a lot of green [ok] messages.
        * If the script appears to pause and prompt for y/n, do not enter any values; the script will answer for you.  
        * Wait until "Drush script finished! ...exiting" before proceeding.**

* Once finished press the `Cntrl` and `d` keys or type `exit` to get out of the apache container

### Testing the site

* Test (QC) the resulting setup by opening a web browser to the `isle.localdomain` URL of the new ISLE sample site (i.e. [https://isle.localdomain](https://isle.localdomain)) and logging in.

* Upon entering [https://isle.localdomain](https://isle.localdomain) within a browser, you will see a SSL error warning that the site is unsafe. It is safe, it simply uses "self-signed" SSL certs. Ignore the error and proceed to the site.

* To log in to the Islandora Drupal Site:
   	* Username: `isle_localdomain_admin`
  	* Password: `isle_localdomain_adminpw2018`

* There is additional information for users and passwords that can be found on the [Test Site Resources](ild_resources.md) page.

### Sample objects for ingest

Courteous of the [Islandora Collaboration Group](https://github.com/Islandora-Collaboration-Group/icg_information) there is a sample set of objects and corresponding metadata that can be used for ingest.

More information can be found on the [Test Site Resources](ild_resources.md) page.

### Post Ingest Search Tip
After ingesting content, you will need to add an Islandora Simple Search block to the Drupal structure.  The default search box that appears on install will only search Drupal content, not Islandora content.

* Structure Menu > Blocks
* Scroll down to find "Islandora Simple Search".  Use dropdown to set to Sidebar Second.
* Save Blocks on the bottom of the page

This process will now allow you to search for ingested objects that have been indexed by SOLR.

---

### Note

* The cronjob setting in the `install_isle_ld_site.sh` script is still commented out as this will need to be flowed into the Docker build process prior. Issue with default Docker root user vs using islandora user. Drupal cron can run properly manually however.

### WARNING

* Please always use the [https://isle.localdomain](https://isle.localdomain) domain to view the site, login etc

* Do not use an IP e.g. `https://10.10.10.130 or https://127.0.0.01` to view the Drupal site, login etc as some components won't work e.g. Large Images using the OpenSeadragon Viewer.

---

### Troubleshooting

#### Port Conflicts
If you encounter an error like this:

`Error starting userland proxy: Bind for 0.0.0.0:xxxx failed: port is already allocated`

then ISLE may have encountered a conflict with the `xxxx` port identified in the error message.

In MacOS, this can frequently be caused by a local Apache or Nginx web server, or local MySQL server.  

You may need to remove or disable these local web servers before you can successfully install ISLE.

* If you have a local Apache web server that ships with most MacOS machines may conflict with port 80, and can usually be disabled from a terminal using these commands:

    * `sudo apachectl stop`
    * `sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null`

* If you have a local `Nginx` web server it may conflict with port 8080, and it can usually be disabled from a terminal using:

    * `sudo nginx -s stop`

Once your web server(s) have been disabled, resume the ISLE install process by repeating your last installation command, presumably `docker-compose up -d`.

#### Non-running Containers
If you don't see five running containers, then stop the running containers with `docker-compose down -d` and start the containers one at a time following the instructions below:

*  MySQL image pull & container launch

    `docker pull islandoracollabgroup/isle-mysql:latest`

    `docker-compose up -d mysql`

*  Fedora image pull & container launch

    `docker pull islandoracollabgroup/isle-fedora:latest`

    `docker-compose up -d fedora`

    (Optional but recommended troubleshooting step)

    * Please note the Tomcat service requires about  one to three minutes to startup and as such if the enduser rushes to the URL supplied below, the service page maytime out or be reported as unreachable. Give it a little time.
    * After spinning up fedora container, check if the Fedora service is running prior to advancing.
    * Navigate to http://fedora:8080/manager/html a popup login prompt should appear.
    * Enter the user name of `admin` and the password of `ild_tc_adm_2018`
    * Upon login a large display of running Tomcat applications should display, scroll down to fedora
    * The application state / status should be true
    * If false appears instead, attempt to restart the fedora service manually.
    * Select the restart button to the right of the status area.
    * If it still fails, review the mounted fedora logs. The docker-compose.yml file will indicate where the logs are located.
    * Using terminal and then entering a command like `tail -n 300 - <path to ISLE project/data/fedora/log/tomcat:/usr/local/tomcat/logs/fedora.log` should display enough information to troubleshoot or restart the entire startup process.

* Solr image pull & container launch

    `docker pull islandoracollabgroup/isle-solr:latest`

    `docker-compose up -d solr`

* Apache image pull & container launch

    `docker pull islandoracollabgroup/isle-apache:latest`

    `docker-compose up -d apache`

* Proxy image pull & container launch

    `docker pull islandoracollabgroup/isle-proxy:latest`

    `docker-compose up -d proxy`
