# BCDS Agent (Ver. 2) Distribution

Inquiries to: takeichi@acm.org
## BCDS Agent Binary
BCDS Agent software package has been distributed for Mac OS X and Windows OS.
* **BCDSAgent2.2.zip**: Distribution Package for Mac OS X
* **BCDSAgent2.2-win.zip**: Distribution Package for Windows 10

The software needs no additional programs to run on machines with these OS. User can install the binary executable code simply by unzipping the downloaded file. Start the program by giving the configuration file for the Agent on the terminal (command line) application. The configuration file is a text file describing the connection information and the code for Application written in SQL. The instruction for providing the configuration file and the tutorial with examples are included in the package.

Current version is working on machines accessible by local network addresses without any specific support of network software library.

The tutorial text describes how to use the software in step-by-step fashion. Following this tutorial leads users to design and run their own Bidirectional Collaboration Data Sharing System.

## BCDS based System Examples
* **Orchestra.zip**: Contains configuration files and databases for an example described in the Tutorial.
* **DejimaXYZ.zip**: Contains configuration files and databases for an exercise described in the Tutorial.
* **Turnaround.zip**: Contains configuration files and databases for modeling Turnaround Deliberation and Public Comment System with Dejima-style data sharing. Explanatory notes with snapshots included.
* **CTDS2.0.zip**: Contains configuration files and databases for the Collaborative Taxi Dispatching System (CTDS). Explanatory notes with snapshots included.

## Using BCDS Agent (Ver.2) on Mac OS X and on Windows 10
* Download **BCDSAgent2.?.zip** for Mac OS X or **BCDSAgent2.?-win.zip** for Windows 10.
* Unzip the downloaded Zip file in any folder.

### Contents distributed:
* **BCDS Agent (Ver.2) Users Guide** explains how to use this ‘Bidirectional Collaborative Data Sharing’ (BCDS) for experiments.
* **BCDSAgent-exe** :  Binary executable code 
* **Orchestra** folder contains configuration files and database for demonstration; this simulates an example of CDSS from ‘Orchestra’ Paper of ACM TODS 38(3), 2013
* **DejimaXYZ** folder contains configuration files and database for constructing Dejima with three Sites.

### Environments
BCDS Agent (Ver.2) was developed under Mac OS X 10.14.5, and was tested on Mac OS X 10.12, too. There will be no problems running on recent versions of Mac OS.  And also it was tested on Windows 10.
### What we need for running BCDS-based systems
We are strongly advised to use **DB Browser for SQLite** to prepare persistent data (database table) of participant Sites of BCDS Agent and to update the View of their own data.

We may use ‘sqlite3’ command line application provided by Mac OS to do these tasks, which might be tedious work to do with ‘sqlite3’ commands.

**DB Browser for SQLite** can be downloaded from https://sqlitebrowser.org
# Overview of Bidirectional Collaborative Data Sharing
### Masato Takeichi 2019/12/29
## Collaborative Data Sharing
  The Collaborative Data Sharing (CDS) system consists of multiple sites that have specific data and share some data with other sites, each running a specific application. The sites connected by the network are executed independently of each other, but the update of the shared data is performed in cooperation. That is, concerning shared data, updates at one site are respected at other sites and are reflected in data held based on the policies of each site.
  As a data-sharing system, a server-client system consisting solely of a server that provides data and a client that receives and processes the data can be considered, but in a CDS system realizes more general Peer-to-Peer (P2P) cooperation.
## Bidirectional Collaborative Data Sharing
  Bidirectional Collaborative Data Sharing (BCDS) is one of the P2P architectures that realizes CDS. By separating the application that processes data and the functions for shared data between sites, the development of applications becomes facilitated. When shared data is updated, the updates are reflected across all sites in the system to ensure the integrity of the shared data. A site in the BCDS system is realized by a BCDS Agent, and multiple BCDS Agents are connected by the network communication using TCP / IP.
  The reason that “BCDS” is named “Bidirectional” prefixed “CDS” is because the policy of providing and updating shared data at the time of execution is realized by the bidirectional transformation.
## Configuration of the collaborative data-sharing system by BCDS system
  When a collaborative data-sharing system is configured with BCDS, a general CDS system can be configured with BCDS Agent, and no special consideration is required for the overall configuration of the system.  But it would also be useful to consider the characteristics of BCDS Agent.
  In designing a system using BCDS, it is particularly important to pay attention to the following points.
* The application and the data for each site that constitutes the system should be specified. When there is data to be shared with other sites, it is assumed that an application is described by an SQL query that processes an SQL table in which the items of the shared data are added to the items of the database maintained by itself. There could be descriptions in languages ​​other than the equivalent of processing by SQL queries.
* For data shared with other sites, determine the data items held by the provider sites and the data items to be sought from them. Shared data can be assumed to be ones that are selected by the SQL query SELECT c_1, c_2,… FROM * WHERE… at the provider. Further, for each data item c_1, c_2,..., it should be specified whether or not to return the result to the provider when the data is changed.
* In the BCDS, each site accepts requests for data provision from other sites. Define the data items that the site can provide to other sites in its data. For each data item shared with other sites, specify whether to return the result of data change at the provided sites (ReadWrite mode) or not to change data at the provided sites (ReadOnly mode). Further, a policy (update policy) for reflecting the change of the data item returned from the provided sites as an update of the data held by itself should be determined. Typically, the results of changes in the data returned from the provided sites are to be reflected in the data held on a first-come, first-served basis.

## BCDS Agent configuration
  The BCDS system consists of multiple BCDS Agents, each of which maintains its database and shares data through cooperation with the outside world. The BCDS system has a mechanism to automatically synchronize the data held by all the related BCDS Agents when one of the BCDS Agents updates data shared by multiple BCDS Agents to maintain consistency. 
  The BCDS Agent has an application function for its data, and also has a server function that provides (part of) its data in response to requests from other BCDS Agents, and a client function that requests the BCDS Agent for data-sharing.  The server function and the client function are executed in parallel independently of the application. Here, the data held by itself includes, in addition to the data in the database owned by the BCDS Agent, shared data provided from other BCDS Agents by the client function. These data are represented in the form of tables in an SQL database, and the shared data items correspond to the columns in the table.
  The data held by the BCDS Agent is updated not only by the application, but also by the client function when the changed data provided by other BCDS Agents, and the server when the data of ReadWrite mode returned from the provided BCDS Agents. Updates related to the client function and updates related to the server function are processed in parallel with the application by each function of the BCDS Agent, so there is no need to explicitly describe it in the application.
  The BCDS Agent provides its data to other BCDS Agents as shared data by the server function. Some shared data items return the result when the provided shared data is changed by the destination (in ReadWrite mode), and others do not return the result even if changed by the destination (in ReadOnly mode).
* The data returned by changing the shared data of ReadWrite mode is reflected as an update of the data based on an updating policy defined by the BCDS Agent. If the same data item is provided to more than one BCDS Agent, not all changes will be reflected as updates to its data. In some cases, the change result at a certain destination may be discarded. This situation occurs because each of the BCDS Agents in the BCDS system allows data to be changed independently of each other. The handling of changes to shared data from multiple destinations and conflicts between them also depends on the updating policy.

The client function of BCDS Agent requests the data held by other BCDS Agents to be provided through the server function of each BCDS Agent. First, it establishes a connection for sending and receiving data by specifying data items to the providing BCDS Agent. At this time, it is specified whether each item is provided in the ReadWrite mode or the ReadOnly mode. If this differs from the service policy of the source BCDS Agent, the connection is rejected. Once the connection is granted, it will receive data thereafter through this connection.
* The client function of the BCDS Agent establishes a connection for the shared data requested by itself and receives data sent by the server function of the provider (when its data is updated). When the data item of the ReadWrite mode is changed by the application or the server function, the process of sending the data item to the provider is performed. Since these processes are performed in parallel with the application, there is no need to explicitly describe them in the application.

A BCDS system composed of the BCDS Agent server function and client function generally has a graph structure based on Peer-to-Peer connections. Data updates by BCDS Agent’s applications are automatically propagated on this structure, and the consistency (Eventual Consistency) of the shared data on the system is maintained.
In the BCDS system, a new BCDS Agent may be added at any time, and the BCDS Agent in the system may be stopped at any time or some client functions may be stopped.
