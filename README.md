#### Contents:
## Table-of-Contents
1. [Install Ambari from **Public Packages**](#i.Install-Ambari-from-Public-Packages)
    1. [Create Ambari User](#1-1-Create-Ambari-User)
    2. [Install Ambari DB Engine](#1-2-Install-Ambari-DB-Engine)
        1. [Package Repository](#1-2-1-Package-Repository)
        2. [Package Installation](#1-2-2-Package-Installation)
        3. [Testing](#1-2-3--Testing)  
    3. [Network Configuration File](#1-3-Network-Configuration-File)  
    4. [IPTables Settings](#1-4-IP-Tables-Settings)
    5. [SELinux and PackageKit Settings](#1-5-SELinux-an-PackageKit-Settings)
    6. [UMASK Settings](#1-6-UMASK-Settings)
    

2. Cat
    1. Siberian
    2. Siamese

#### Contents:
- 1 - [Install Ambari from **Public Packages**](#i.Install-Ambari-from-Public-Packages)
    - 1.1 - [Create Ambari User](#1-1-Create-Ambari-User)
    - 1.2 - Install Ambari DB Engine:
         - [1.2.1 - Package Repository](#1-2-1-Package-Repository)
         - [1.2.2. - Package Installation](#1-2-2-Package-Installation)
         - [1.2.3. - Testing](#1-2-3--Testing)
            - 1.2.3.1 [Testing - alone](#1-2-3--Testing)
          
    - 1.3 - [Network Configuration File](#1-3-Network-Configuration-File)
    - 1.4 - [IP Tables Settings](#1-4-IP-Tables-Settings)
    - 1.5 - [SELinux and PackageKit Settings](#1-5-SELinux-an-PackageKit-Settings)
    - 1.6 - [UMASK Settings](#1-6-UMASK-Settings)

- 2) - [Gather required Ambari and Solr parameters](#0-gather-params)
- 3) - [Backup Solr Collections](#ii.-backup-collections-(ambari-2.6.x-to-ambari-2.7.x))
    - a.) If you have Ranger Ambari service with Solr audits:
        - [1. Backup Ranger collection](#ii/1.-backup-ranger-collection)
        - [2. Backup Ranger configs on Solr ZNode](#ii/2.-backup-ranger-configs-on-solr-znode)
        - [3. Delete Ranger collection](#ii/3.-delete-ranger-collection)
        - [4. Upgrade Ranger Solr schema](#ii/4.-upgrade-ranger-solr-schema)
    - b.) If you have Atlas Ambari service:
        - [5. Backup Atlas collections](#ii/5.-backup-atlas-collections)
        - [6. Delete Atlas collections](#ii/6.-delete-atlas-collections)
    - c.) If you have Log Search Ambari service:
        - [7. Delete Log Search collections](#ii/7.-delete-log-search-collections)
        - [8. Delete Log Search Solr configs](#ii/8.-delete-log-search-solr-configs)
- [IV. Upgrade Ambari Infra Solr package](#iii.-upgrade-infra-solr-packages)
- [V. Re-create Solr Collections](#iv.-re-create-collections)
- [VI. Migrate Solr Collections](#v.-migrate-solr-collections)
    - a.) If you have Ranger Ambari service with Solr audits:
        - [1. Migrate Ranger Solr collection](#v/1.-migrate-ranger-collections)
    - b.) If you have Atlas Ambari service:
        - [2. Migrate Atlas Solr collections](#v/2.-migrate-atlas-collections)
- [VII. Restore Solr Collections](#vi.-restore-collections)
    - a.) If you have Ranger Ambari service with Solr audits:
        - [1. Restore old Ranger collection](#vi/1.-restore-old-ranger-collection)
    - b.) If you have Atlas Ambari service:
        - [4. Restore old Atlas collections](#vi/4.-restore-old-atlas-collections)
- [VIII. Restart Solr Instances](#vii.-restart-infra-solr-instances)
- [IX. Transport old data to new collections](#viii.-transport-old-data-to-new-collections)
    - a.) If you have Ranger Ambari service with Solr audits:
        - [1. Transport old data to Ranger collection](#viii/1.-transport-old-data-to-ranger-collection)
    - b.) If you have Atlas Ambari service:
        - [2. Transport old data to Atlas collections](#viii/2.-transport-old-data-to-atlas-collections)
- [Happy Path](#happy-path)
- [APPENDIX](#appendix)


#### <a id="ii/1.-backup-ranger-collection">III/1. Backup Ranger collection</a>

-----------------------------
### <a id="i.Install-Ambari-from-Public-Packages">I. Install Ambari from Public Packages</a>
The last **public version** of Ambari packages is the **2.7.4.0-118**. The latest Ambari version is the **2.7.5.0.0** and is maintained by [Cloudera](https://docs.cloudera.com/HDPDocuments/Ambari/Ambari-2.7.5.0/index.html). It's a commercial distribution and cannot be using without purchase a paying account.

This procedure is the simplest and preferred installation mode.
