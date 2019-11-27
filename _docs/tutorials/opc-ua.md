---
title: OPC UA in Ventus Cloud
description: How to use OPC UA Components in Ventus Cloud
tags: [ featured, tutorial ]
# permalink: /opc-ua/
---
# How to use OPC UA Components in Ventus Cloud
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

* TOC
{:toc}

## What is OPC UA

**OPC Foundation** is an organization that provides broad opportunities for interoperability in the field of automation by creating and maintaining an open communication standard.

The **O**pen **P**latform **C**onfigurations **U**nified **A**rchitecture (**OPC UA**), released in 2008, is a standard technology for industrial automation communication. It is a platform that integrates all technology standards such as OPC DA, HA, and AE, etc. It features reading and writing real-time data, accessing historical data, and event/warning publishing functions.

This multi-layered approach accomplishes the original design specification goals of:

**1) Functional equivalence**: all COM OPC Classic specifications are mapped to UA  

Building on the success of OPC Classic, OPC UA was designed to enhance and surpass the capabilities of the OPC Classic specifications. OPC UA is functionally equivalent to OPC Classic, yet capable of much more:
   * *Discovery*: find the availability of OPC Servers on local PCs and/or networks  
   * *Address space*: all data is represented hierarchically (e.g. files and folders) allowing for simple and complex structures to be discovered and utilized by OPC Clients  
   * *On-demand*: read and write data/information based on access-permissions  
   * *Subscriptions*: monitor data/information and report-by-exception when values change based on a client’s criteria  
   * *Events*: notify important information based on client’s criteria  
   * *Methods*: clients can execute programs, etc. based on methods defined on the server

Integration between OPC UA products and OPC Classic products is easily accomplished with COM/Proxy wrappers that are available in the download section.

**2) Platform independence**: from an embedded micro-controller to cloud-based infrastructure 

Given the wide array of available hardware platforms and operating systems, platform independence is essential. OPC UA functions on any of the following and more:
   * *Hardware platforms*: traditional PC hardware, cloud-based servers, * PLCs, micro-controllers (ARM etc.)  
   * *Operating Systems*: Microsoft Windows, Apple OSX, Android, or any distribution of Linux, etc.  

OPC UA provides the necessary infrastructure for interoperability across the enterprise, from machine-to-machine, machine-to-enterprise and everything in-between. 

**3) Secure**: encryption, authentication, and auditing  

One of the most important considerations in choosing a technology is security. OPC UA is firewall-friendly while addressing security concerns by providing a suite of controls:
   * *Transport*: numerous protocols are defined providing options such as the ultra-fast OPC-binary transport or the more universally compatible JSON over Websockets, for example  
   * *Session Encryption*: messages are transmitted securely at various encryption levels  
   * *Message Signing*: with message signing the recipient can verify the origin and integrity of received messages  
   * *Sequenced Packets*: exposure to message replay attacks is eliminated with sequencing  
   * *Authentication*: each UA client and server is identified through X509 certificates providing control over which applications and systems are permitted to connect with each other  
   * *User Control*: applications can require users to authenticate (login credentials, certificate, web token etc.) and can further restrict and enhance their capabilities with access rights and address-space “views”  
   * *Auditing*: activities by user and/or system are logged providing an access audit trail  

**4) Extensible**: ability to add new features without affecting existing applications  

The multi-layered architecture of OPC UA provides a “future proof” framework. Innovative technologies and methodologies such as new transport protocols, security algorithms, encoding standards, or application-services can be incorporated into OPC UA while maintaining backwards compatibility for existing products. UA products built today will work with the products of tomorrow.

**5) Comprehensive** information modeling: for defining complex information  

The OPC UA information modeling framework turns data into information. With complete object-oriented capabilities, even the most complex multi-level structures can be modeled and extended.

This framework is THE fundamental element of OPC Unified Architecture. It defines the rules and base building blocks necessary to expose an information model with OPC UA. While OPC UA already defines several core models that can be applied in many industries, other organizations build their models upon them, exposing their more specific information with OPC UA.

OPC UA also defines the necessary access mechanisms to information models.

   * Look-up mechanism (browsing) to locate instances and their semantic  
   * Read and write operations for current data and historical data  
   * Method execution  
   * Notification for data and events

For Client-Server communication the full range of information model access is available via services and in doing so follows the design paradigm of service-oriented architecture (SOA), with which a service provider receives requests, processes them and sends the results back with the response.

Publish-Subscribe (PubSub), provides an alternative mechanism for data and event notification. While in Client-Server communication each notification is for a single client with guaranteed delivery, PubSub has been optimized for many-to-many configurations.

With PubSub, OPC UA applications do not directly exchange requests and responses. Instead, Publishers send messages to a Message Oriented Middleware, without knowledge of what, if any, Subscribers there may be. Similarly, Subscribers express interest in specific types of data, and process messages that contain this data, without a need to know where it originated from.

*By using the OPC UA controller, factories and enterprises can:*

1. Create the web-based vertical integration of both the upstream and the downstream system.
2. Integrate onsite data in the back-end system for further analysis.
3. Horizontally connect each subsystem.

## How to use OPC Components  

This setup is using 3 containers, which are all available on Docker Hub.

### General Preparation  

* Define an environment variable `$env:_HUB_CS` and set it to your iothubowner connection string of the IoTHub you are using  
* If you are using docker to run the setup, create a docker bridge network with name *iot_edge* with `docker network create iot_edge`. Verify with `docker network ls`   
* The samples are using Windows as the host OS, they are using Linux containers (means that LCOW is used) and they use PowerShell as the shell  
* If you are using native executables, ensure you have installed Visual Studio, git and the required .NET SDKs  
* If you are using configurations with Docker containers. Install Docker and ensure that you have shared the drive where `<reporoot>` is located via the Docker Settings dialog  
* Define an environment variable `$env:_REPO_ROOT` and set it to the full qualified directory name of the root of this repository

### OPC Publisher  

**OPC Publisher** allows to send telemetry from OPC UA server systems to IoTHub. 
A docker container of the component is hosted [here](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) and can be pulled by:
```
docker pull mcr.microsoft.com/iotedge/opc-publisher
```
**Configurations with locally built docker containers:**  

Run the following PowerShell commands in the root of the repository:
```
git clone https://github.com/Azure-Samples/iot-edge-opc-plc
cd ./iot-edge-opc-plc
docker build -t opc-plc .
docker run -h opcplc --name opcplc --network iot_edge --expose 51210 -p 51210:51210 opc-plc --aa
```

In a second PowerShell command window run the following commands in the root of the repository:
```
git clone https://github.com/Azure/iot-edge-opc-publisher
cd ./iot-edge-opc-publisher
docker build -t opc-publisher .
docker run -h publisher --name publisher --network iot_edge -v "$($env:_REPO_ROOT):/appdata" -e "_HUB_CS=$env:_HUB_CS" opc-publisher publisher --aa --pf /appdata/publishednodes_opcplc.json
```
*Verification*:  
Verify that opc-publisher is connected to opc-plc and that 7 items are monitored. You should see something like this in the log output of opc-publisher:
```console
[18:52:08 INF] Connect and monitor session and nodes on endpoint 'opc.tcp://opcplc:50000/'.
[18:52:10 INF] Create secured session for endpoint URI 'opc.tcp://opcplc:50000/' with timeout of 10000 ms.
[18:52:10 INF] Certificate 'CN=OpcPlc' will be trusted, since the autotrustservercerts options was specified.
[18:52:10 INF] Session successfully created with Id ns=3;i=1792545597.
[18:52:10 INF] The session to endpoint 'opc.tcp://opcplc:50000/' has 4 entries in its namespace array:
[18:52:10 INF] Namespace index 0: http://opcfoundation.org/UA/
[18:52:10 INF] Namespace index 1: urn:OpcPlc:opcplc
[18:52:10 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
[18:52:10 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
[18:52:10 INF] The server on endpoint 'opc.tcp://opcplc:50000/' supports a minimal sampling interval of 0 ms.
[18:52:10 INF] Created subscription with id 1 on endpoint 'opc.tcp://opcplc:50000/'
[18:52:10 INF] Create subscription on endpoint 'opc.tcp://opcplc:50000/' requested OPC publishing interval is 0 ms. (revised: 0 ms)
[18:52:10 INF] Start monitoring items on endpoint 'opc.tcp://opcplc:50000/'. Currently monitoring 0 items.
[18:52:10 INF] Done processing unmonitored items on endpoint 'opc.tcp://opcplc:50000/' took 96 msec. Now monitoring 7 items in subscription with id '1'.
```

**Configurations with prebuilt docker containers**  

Run the following PowerShell commands in the root of the repository:
```
docker run -h opcplc --name opcplc --network iot_edge --expose 51210 -p 51210:51210 mcr.microsoft.com/iotedge/opc-plc --autoaccept
```

In a second PowerShell command window run the following commands in the root of the repository:
```
docker run -h publisher --name publisher --network iot_edge -v "$($env:_REPO_ROOT):/appdata" -e "_HUB_CS=$env:_HUB_CS" mcr.microsoft.com/iotedge/opc-publisher publisher --aa --pf /appdata/publishednodes_opcplc.json
```

*Verification*:  
Verify that opc-publisher is connected to opc-plc and that 7 items are monitored. You should see something like this in the log output of opc-publisher:
```console
[19:10:51 INF] Connect and monitor session and nodes on endpoint 'opc.tcp://opcplc:50000/'.
[19:10:52 INF] Create secured session for endpoint URI 'opc.tcp://opcplc:50000/' with timeout of 10000 ms.
[19:10:52 INF] Certificate 'CN=OpcPlc' will be trusted, since the autotrustservercerts options was specified.
[19:10:53 INF] Session successfully created with Id ns=3;i=474450433.
[19:10:53 INF] The session to endpoint 'opc.tcp://opcplc:50000/' has 4 entries in its namespace array:
[19:10:53 INF] Namespace index 0: http://opcfoundation.org/UA/
[19:10:53 INF] Namespace index 1: urn:OpcPlc:opcplc
[19:10:53 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
[19:10:53 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
[19:10:53 INF] The server on endpoint 'opc.tcp://opcplc:50000/' supports a minimal sampling interval of 0 ms.
[19:10:53 INF] Created subscription with id 1 on endpoint 'opc.tcp://opcplc:50000/'
[19:10:53 INF] Create subscription on endpoint 'opc.tcp://opcplc:50000/' requested OPC publishing interval is 0 ms. (revised: 0 ms)
[19:10:53 INF] Start monitoring items on endpoint 'opc.tcp://opcplc:50000/'. Currently monitoring 0 items.
[19:10:53 INF] Done processing unmonitored items on endpoint 'opc.tcp://opcplc:50000/' took 93 msec. Now monitoring 7 items in subscription with id '1'.> ```
```

**Configurations using docker-compose**  

Run the following PowerShell commands in the root of the repository:
```
docker-compose -f simple.yml up
```

*Verification*:  
Verify that opc-publisher is connected to opc-plc and that 7 items are monitored. You should see something like this in the log output of opc-publisher:
```console
publisher    | [19:30:59 INF] Connect and monitor session and nodes on endpoint 'opc.tcp://opcplc:50000/'.
publisher    | [19:31:00 INF] Create secured session for endpoint URI 'opc.tcp://opcplc:50000/' with timeout of 10000 ms.
publisher    | [19:31:00 INF] Certificate 'CN=opcplc' will be trusted, since the autotrustservercerts options was specified.
publisher    | [19:31:01 INF] Session successfully created with Id ns=3;i=736575751.
publisher    | [19:31:01 INF] The session to endpoint 'opc.tcp://opcplc:50000/' has 4 entries in its namespace array:
publisher    | [19:31:01 INF] Namespace index 0: http://opcfoundation.org/UA/
publisher    | [19:31:01 INF] Namespace index 1: urn:opcplc
publisher    | [19:31:01 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
publisher    | [19:31:01 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
publisher    | [19:31:01 INF] The server on endpoint 'opc.tcp://opcplc:50000/' supports a minimal sampling interval of 0 ms.
publisher    | [19:31:01 INF] Created subscription with id 1 on endpoint 'opc.tcp://opcplc:50000/'
publisher    | [19:31:01 INF] Create subscription on endpoint 'opc.tcp://opcplc:50000/' requested OPC publishing interval is 0 ms. (revised: 0 ms)
publisher    | [19:31:01 INF] Start monitoring items on endpoint 'opc.tcp://opcplc:50000/'. Currently monitoring 0 items.
publisher    | [19:31:01 INF] Done processing unmonitored items on endpoint 'opc.tcp://opcplc:50000/' took 94 msec. Now monitoring 7 items in subscription with id '1'.
```

### OPC PLC server  

An OPC UA server using the [OPC UA .NET Standard Console Reference stack](https://github.com/OPCFoundation/UA-.NETStandard), with some nodes generating random data and data with anomalies. The source could be found here. The nodeset of this server contains various nodes which can be used to generate random data or anomalies.

A docker container of the component is hosted [here](https://hub.docker.com/_/microsoft-iotedge-opc-plc) and can be pulled by:
```
docker pull mcr.microsoft.com/iotedge/opc-plc
```

**Features**  
The following nodes are part of the PLC simulation:
* with alternating boolean  
* random signed 32-bit integer  
* random unsigend 32-bit integer  
* a sine wave with a spike anomaly  
* a sine wave with a dip anomaly  
* a value showing a positive trend  
* a value showing a negative trend

By default everything is enabled, please use command line options to disable certain anomaly or data generation features. Additionally to simulation mode, a JSON configuration file, which defines nodes to be populated.

**Node population from JSON configuration file**    

If the module (application) is started with argument --nodesfile then specified JSON configuration file is loaded. Nodes defined in JSON file will be published by the server. This enables another OPCUA-client application to set the state/value of the node. Please note that nodes specified in JSON file are NOT a part of simulation. They remain visible in unchanged state as long some OPCUA-client application changes their status. Following command shows how to start the module (application):

```
dotnet opcplc.dll --at X509Store --nodesfile nodesfile.json
```

Configuration file looks as described below:
```
{
  "NamespaceId": 7,
  "Name": "MyTelemetry",
  "NodeList": {
   [
      {
        "NodeId": "7123",
        "Name": "Temperature",
        "DataType": "Float",
        "ValueRank": -1,
        "AccessLevel": "CurrentReadOrWrite",
        "Description": "My property 1"
      },
      {
        "NodeId": "7124",
        "Name": "Oscilations",
        "DataType": "Float",
        "ValueRank": -1,
        "AccessLevel": "CurrentReadOrWrite",
        "Description": "My property 2"     
      }
    ]
  }
}
```
*NodeList* defines the list of nodes, which will be published by emulated server. Nodes specified in the list can be browsed and changed by OPC-UA applications. This enables developers to easy implement and test OPC-UA client applications.

*NodeId*: Specifies the identifier of the node. This is the NodeId in the NamespaceId. 
*Name*: The name of the sensor.  
*DataType*: The OPC UA valid type. It specifies one of types defined by BuiltInType.  
*ValueRank*: As defined by type ValueRanks.   
*AccessLevel*: Specifies one of access levels defined by type AccessLevels.  *Description*: Description of the node.

**Notes**  

X.509 certificates releated:

* Running on Windows natively, you can not use an application certificate store of type Directory, since the access to the private key fails. Please use the option `--at X509Store` in this case.  
* Running as Linux docker container, you can map the certificate stores to the host file system by using the docker run option `-v <hostdirectory>:/appdata`. This will make the certificate persistent over starts.  
* Running as Linux docker container and want to use an X509Store for the application certificate, you need to use the docker run option `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` and the application option `--at X509Store`.

### OPC Client  

OPC UA client able to run OPC operations on an OPC UA server.
The client allows run single or recurring operations targeting an OPC UA server to:
* test connectivity by reading the current time node  
* read node values  

By default opc-client is testing the connectivity to `opc.tcp://opcplc:50000`. This can be disabled or changed to a different endpoint via command line.

The implementation is based on *.NET Core* so it is cross-platform and recommended hosting environment is docker.

A docker container of the component is hosted [here](https://hub.docker.com/_/microsoft-iotedge-opc-client) and can be pulled by:
```
docker pull mcr.microsoft.com/iotedge/opc-client
```

### OPC Testserver  

An OPC UA server, based on the OPC UA .NET Standard Console Reference server. The nodeset of this server contains various nodes used by OPC Testclient to test OPC Publisher functionality.

This OPC UA server is based on the source code of the Console reference server in the [OPC UA .NET Standard github repository of the OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard). The original Console reference server source can be found in the subdirectory `./SampleApplications/Workshop/Reference` of the above mentioned repository.

A docker container of the component is hosted [here](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testserver/) on Docker Hub.

The server can be reached via its endpoint at `opc.tcp://<localhostname>:62541/Quickstarts/ReferenceServer`.

By specifying the commandline option `-a` it accepts automatically all incoming requests from clients (Note: Please be aware of the implied security risk when using this option).

### OPC Testclient  

An OPC UA client, based on the [OPC UA .NET Standard Console client], which is calling OPC UA methods of OPC Publisher to publish/unpublish nodes in the testserver. The source could be found here. The client does access OPC Publisher to publish/unpublish nodes of OPC Testserver.

This OPC UA client is based on the source of the Console client source in the [OPC UA .NET Standard github repository of the OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard). The original Console client could be found in the subdirectory `./SampleApplications/Samples/NetCoreConsoleClient` of the repository.

A docker container of the component is hosted [here](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testclient/) on Docker Hub.

Command line is:  
```
Usage: iot-edge-opc-publisher-testclient.exe [<options>]
     OPC Publisher test client. Requires iot-edge-opc-publisher-testclient and OPC Publisher.
     To exit the application, just press CTRL-C while it is running.
     Options:
       -h, --help                 show this message and exit
       -a, --autoaccept           auto accept certificates (for testing only)
           --ne, --noexclusive    do not execute any exclusive tests
           --tt, --testtime=VALUE the number of seconds to run the different tests
           --tu, --testserverurl=VALUE
                                  URL of the OPC UA test server
           --pu, --publisherurl=VALUE
                                  URL of the OPC Publisher (required when using OPC
                                    UA methods)
           --o1, --opcmethods     use the OPC UA methods calls to test
           --ic, --iotHubConnectionString=VALUE
                                  IoTHub owner connectionstring
           --id, --iothubdevicename=VALUE
                                  IoTHub device name of the OPC Publisher (required
                                    when using IoT methods)
           --im, --iothubmodulename=VALUE
                                  IoTEdge module name of the OPC Publisher which
                                    runs in IoTEdge specified by im/iothubdevicename(
                                    required when using IoT methods and IoTEdge)
           --i1, --iotHubMethods  use IoTHub direct methods calls to test
           --lf, --logfile=VALUE  the filename of the logfile to use.
                                    Default: './acfbb604c946-ìot-edge-opc-publisher-
                                    testclient.log'
           --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                    info, debug, verbose).
                                    Default: info
```

*The test include:*  
* Run publish/unpublish sequences using NodeId and ExpandedNodeId syntax  
* Run longhaul test with random publish/unpublish calls of nodes                                    