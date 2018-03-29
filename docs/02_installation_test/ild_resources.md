### WARNING
Do not use an IP e.g. `https://10.10.10.130` to view the Drupal site, login etc as somethings won't work e.g. Large Images using the OpenSeadragon Viewer.

Please always use the [https://isle.localdomain](https://isle.localdomain) domain to view the site, login etc


---

### Sample Objects for ingest.
Courteous of the [Islandora Collaboration Group](https://github.com/Islandora-Collaboration-Group/icg_information) there is a sample set of objects and corresponding metadata (examples organized by solution pack, including zips for quicker bulk ingestion) that can be used to test ingest.

* [https://github.com/Islandora-Collaboration-Group/islandora-sample-objects](https://github.com/Islandora-Collaboration-Group/islandora-sample-objects)

You are free to clone this repository to your local laptop or desktop and ingest as required.

If you are not familiar with ingest, one can follow instructions [here](https://wiki.duraspace.org/display/ISLANDORA/How+to+Add+an+Item+to+a+Digital+Collection) or start with the [Getting Started with Islandora section](https://wiki.duraspace.org/display/ISLANDORA/Getting+Started+with+Islandora) of the official Islandora documentation.

---

### Users and passwords

* `islandora` user on the ISLE host server uses `islandora` as the password.

* Some of the information below is for accessing the non Drupal site admin panels and resources only. (optional). In this context, `hostip` below can mean either
     * the IP address e.g. `http://10.10.10.130` of the Vagrant or Non-Vagrant Host VM (_CentOS / Ubuntu_)
     * the IP address e.g. `127.0.0.1` for `Docker for Mac`

#### 1. MySQL container
| Compose Service Name | Container Name  | Software      | Ports         |
| :-------------:      | :-------------: | ------------- | ------------- |      
| mysql                | isle-mysql-ld   | MySQL 5.6     | 3306          |


| Account        | Password              | Database         | Perms                         |
| -------------  | -------------         | -------------    | -------------                 |      
| root           | ild_mysqlrt_2018      | **ALL**          | **ALL**                       |
| fedora_admin   | ild_feddb_2018        | fedora3          | **All** except `Grant` option |
| isle_ld_user   | isle_ld_db2018        | isle_ld          | **All** except `Grant` option |

---

#### 2. Fedora container
| Compose Service Name | Container Name  | Software      | Ports                                            |
| :-------------:      | :-------------: | ------------- | -------------                                    |      
| fedora               | isle-fedora-ld  | see below     | 8080, 80 (on container) mapped to 8777 (on host) |


| Software                         | Version           |
| -------------                    | -------------     |
| Fedora                           | 3.8.1             |
| Apache                           | 2.4.7             |
| Drupalfilter                     | 3.8.1             |
| Gsearch (w/remote SOLR plugin)   | 2.8+ (DG patched) |
| (DG) GSearch Extensions          | 0.13              |
| (DG) Islandora Transforms (XSLTs)| as of 3/2018      |
| Tomcat                           | 7.x               |  
| Oracle JDK                       | 8.x               |
| Djatoka                          | 1.1               |

| Account           | Password                      | Service       | URL           |
| -------------     | -------------                 | ------------- | ------------- |      
| fedoraAdmin       | ild_fed_admin_2018            | Fedora        | http://hostip:8080/fedora/describe                          |
| fedoraIntCallUser | ild_fed_IntCallUser_2018      | Fedora        | http://hostip:8080/fedora/objects                           |
| anonymous         | anonymous                     | Fedora        | ---                                                         |
| fgsAdmin          | ild_fgs_admin_2018            | Gsearch       | http://hostip:8080/fedoragsearch/rest?operation=updateIndex |
| admin             | ild_tc_adm_2018               | Tomcat        | http://hostip:8080/manager/html                             |
| manager           | ild_tc_man_2018               | Tomcat        | http://hostip:8080/manager/html                             |
| --                | --                            | Djatoka       | http://hostip:8080/adore-djatoka/                           |

---

#### 3. Solr container

| Compose Service Name | Container Name  | Software      | Ports                                              |
| :-------------:      | :-------------: | ------------- | -------------                                      |      
| solr                 | isle-solr-ld    | see below     | 8080 (on container) mapped to 8091 (on host)       |


| Software               | Version       |
| -------------          | ------------- |
| Solr                   | 4.10.4        |
| Tomcat                 | 7.x           |  
| Oracle JDK             | 8.x           |
| (DG) Basic Solr Config | 4.10.x branch |

| Account           | Password        | Service       | URL                             |
| -------------     | -------------   | ------------- | -------------                   |
| admin             | ild_tc_adm_2018 | Tomcat        | http://hostip:8091/manager/html |
| manager           | ild_tc_man_2018 | Tomcat        | http://hostip:8091/manager/html |
| --                | --              | Solr          | http://hostip:8091/solr/        |

---

#### 4. Apache container

| Compose Service Name | Container Name  | Software      | Ports         |
| :-------------:      | :-------------: | ------------- | ------------- |      
| apache               | isle-apache-ld  | see below     | 80, 443       |


| Software      | Version       |
| ------------- | ------------- |
| Apache        | 2.4.7         |
| Oracle JDK    | 8.x           |
| Djatoka       | 1.1           |
| Drupal        | 7.57          |
| PHP           | 5.6           |
| Islandora     | 7.x           |

| Account                | Password                      | Service               | URL                                                  |
| -------------          | -------------                 | -------------         | -------------                                        |
| isle_localdomain_admin | isle_localdomain_adminpw2018  | Drupal site admin     | [https://isle.localdomain](https://isle.localdomain) |

---

#### 5. Proxy container
| Compose Service Name | Container Name  | Software      | Ports         |
| :-------------:      | :-------------: | ------------- | ------------- |
| proxy                | isle-proxy      | Nginx 1.13    | 80, 443       |
