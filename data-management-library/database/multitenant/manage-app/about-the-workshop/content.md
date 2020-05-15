# Oracle Multitenant workshop

## Workshop Overview

**[Oracle Multitenant](https://github.com/oracle/learning-library/issues)** is the architecture for the next-generation database cloud. It delivers isolation, agility and economies of scale. A multitenant container database can hold many pluggable databases. An existing database can simply be adopted with no application changes required. Oracle Multitenant fully complements other options, including Oracle Real Application Clusters and Oracle Active Data Guard.

This hands-on workshop focuses on
* Multitenant Basics : This is a 4 hour workshop that helps DBAs to perform operations as below.
    * Create, Drop, Unplug and Plug PDBs.
    * Includes Hot clone, PDB refresh and PDB relocate.
* Multitenant Application Containers : This is a 3 hour workshop. Once the Basic is mastered, DBAs can architect the application to take advantage of Multitenant specific features.
    * Application Architecture, Upgrde, Porxy PDBs , Syncronizing Application and Version control.
* Multitenant Tenant Isolation : Helps DBA mange resource allocation, isolation of pluggable database.
    * Isolation features like Database Firewall
    * Memory, CPU and IO isolation.

## Workshop Requirements

* Access to Oracle Cloud Infrastructure
    * Provided by the instructor for instructor-led workshops
* Access to a laptop or a desktop
    * To access the OCI server through tools like putty and sqldeveloper.


## Multitenant Data Consolidation for mordern architecture.

###  Value proposition for Multitenant

<p>It is common to see Software and Hardware platform evole over time to be more efficiancy and performant at lower cost.
Deployment  environments using Hardware and sofware frameworks like Kubernetes and Docker are taking away market share from treditional Virtual Images and Bear-Metal servers.

In terme of Development paradim, people are moving from Single Monolitic Application to MicroServices and Continous Development and deployment models.

In terms of Datatype, the adoption of multiple formats like XML, JSON, Relational tabales,Text docs, Spacial, Big Data , IOTs and NOSQL are getting popular.

These modern designs are quickly becoming popular because it is easly to setup and accessible due to Cloud providers who readily provide the platform services.

A problem arises in these designs if a DBA of a production system is not involved in the Design of these newer applications. If a Database to store data is kept close to the thin application layer and have different DB stores for different data formats, it usually becomes App tier heavy and will consume more CPU resources and more managements steps to tune,Upgrade and provide High avaibility, Disaster Recovery and scalability. Often, the DBA now has to be proficient in more than one Data Stores. And even the best of breed Data store which is Freeware/shareware lacks the rich features of Oracle. </P>
![](images/MicroservicesInDocker.png " ")

This is where Oracle Database Multitenant and DB features comes into play. Oracle DB has the ability to store all the Modern Datatypes like JSON, XML, IOTs, nosql, Big data format like parquet files, Text docs, Spacial all within the DB. In addition , it has built in features of partitioning,Machine Learning, Tuning,etc.

Multitenant feature can help address the Modern archtecture of thin application frameworks in Dockers access a single PDB (data store) if the Data store needs to be isolated. However, the rich featues Multitenat can provision over 4000 PDBs in a single DB Container and can address any number of Applications.

![](images/MicroServiceCDB.png " ")

Oracle Multitenant is best suited to be part of every mordern application archtecture and both in On-prem and in the cloud. It is recommented to put all Data store requirements seperate from the application tier so that there is better resource utilization, management, scalability and availability. Oracle Recommends the most flexible architecture and recommends to put all Data stores outside the Application tier.

![](images/MordernArchtecture.png " https://www.oracle.com/database/technologies/multitenant.html")
