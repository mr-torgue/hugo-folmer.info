---
title: 'Opensource SIEM: MozDef'
description: 
slug: siem-mozdef
date: '2021-11-15'
categories:
    - Guides
tags:
    - SIEM
    - MozDef
---

I recently encountered an opensource SIEM called MozDef. It is made by Mozilla and according to website their goal is "The Mozilla Defense Platform (MozDef) seeks to automate the security incident handling process and facilitate the real-time activities of incident handlers.". Being an opensource software fan it hit me that I know of several opensource security tools, like OSSEC or wazuh for HIDS, zeek(bro) or snort for NIDS, ClamAV for antivirus, PFsense for firwalls, but I never encountered an opensource SIEM and I haven't heard from any companies which use such a thing. So I wanted to see what MozDef is all about. I even want to take it a step further: "Is it possible to build a fully opensource SIEM?".

Let me give you some background where I am coming from. I have some experience with commercial SIEM's. The idea behind a SIEM is great although I think the term becomes too broad and is becoming a security buzzword. It is sometimes sold as the perfect solution for security problems and even worse some people believe it is a perfect solution. This is not true, A SIEM is not a plug-and-play solution, it requires a lot of effort in configuration and content creation before you get your moneys worth out of it.

Anyways, this article will mainly focus on MozDef and how it compares to other SIEM's. I will try to answer the following questions:
1. Can MozDef be used as a SIEM in real businesses?
2. What are the pros and cons of MozDef?
3. How does MozDef compare with commercial SIEM's.
This article will give a short introduction about SIEM's in which I will also give some personal opinions on the topic. Then we will shortly discus MozDef based on its online documentation. I will do a short hands-on introduction to MozDef in which I will set it up, generate some logs and explore MozDef in more detail. After that I will give a verdict in which I will answer the questions above. In the conclusion I will give some thoughts about the first question: "Is it possible to build a fully opensource SIEM?".

## What makes a SIEM
According to the [NIST glossary](https://nvlpubs.nist.gov/nistpubs/ir/2013/NIST.IR.7298r2.pdf) a SIEM is defined as: "Application that provides the ability to gather security data from information system components and present that data as actionable information via a single interface.". This is ofcourse pretty vague, so it is better to look at some common SIEM capabilities. [Wikipedia](https://en.wikipedia.org/wiki/Security_information_and_event_management) lists several capabilities:
1. Data Aggregation -- the capability to collect data from multiple sources and combine them in one system.
2. Correlation -- the capability to tie data together.
3. Alerting -- the ability to evaluate if an event is noteworthy and send out alerts.
4. Dashboards -- the capability to summarize important data into dashboards.
5. Compliance -- used for gathering compliance data.
6. Retention -- storing data for a given retention time.
7. Forensic analysis -- the ability to search the data on specific timespans and other filters.

A SIEM has a lot in common with log management. It is probably fair to see that it is build on top of log management.

Basically A SIEM is used for security monitoring. A company usually has several security use-cases which it wants to monitor. For example if you know that a certain IP address is malicious you could create a use-case in which you define that you want an alert when someone or something accesses this IP address. Whilst some SIEM's come with predefined use-cases, don't assume that they work for your scenario. Use-cases can vary between organisations. It is a good idea to document properly what you want to monitor on. You can use the [mitre attack framework](https://attack.mitre.org/) and existing risk analysis as input for your use-cases.

SIEM's are also known to have a relation with incident response. If a use-case triggers you will usually follow a process. This process usually involves researching if it is something significant. If it is, it gets escalated to an incident. In the incident phase you usually contact other personal to fix the problem. Some SIEM's are shipped with functionality for investigations and incidents. It makes sense because information from the SIEM is going to be used. 

As can be seen, there are a lot of functions in a SIEM. Therefore I think it is a good idea to seperate the functions a bit. In my opinion the terms log management and SIEM should be seperated. In a SIEM you should not be able to see all incoming data. You should only see things you find interesting. You do this by creating use-cases. In a SIEM you should be able to see aggregated views of the stored data in dashboards and reports. You might ask yourself what the benefits are of this, wouldn't you miss a lot of data. First of all you don't get overflooded with data, I think this gives a better overview of the overall monitoring status. Secondly a SIEM should be build on a log manager where you can access all the data if you want.

A log manager on the other hand is a tool which you keep around to store logs for a given period of time, for example 180days. The logs are stored orderly. It has to be documented with timestamps, source, hashes (for integrity) and more. A logger stores sensitive information and because it gets stored in a single location(or cluster) which makes it even more sensitive, it should be protected very well. So make sure that logs remain intact and are stored and send confidentiallity. In a log manager you should focus on log management, not security. A logger is a great tool to search for anomalies. It is also great for forensic investigations, once you have some indicators what to look for. 

## MozDef
MozDef is an opensource SIEM to counter commercial SIEMS like splunk, ArcSight, QRadar, etc. MozDef is uses elasticsearch for storage. This seems like a good idea: elastic can run as a cluster so it is scalable and redundant. It is also used in profesional environments. MozDef expects logging from shippers in json format. Shippers you can use are logstash, rsyslog, rabbitmq and many more. The fact that is builds on well known and tested software is positive. The architecture for MozDef looks like this:

MozDef places itself between elastic and the shippers like logstash. The MozDef frontend parses and standardizes the logs received from the shippers.
There are two things I don't like about this. First of all by doing this you limit the use of elastic search, the sole purpose of the elastic search cluster is to store MozDef data. What if you want to use you existing elastic cluster for this? Wouldn't it make more sense to let the shippers connect directly to elastic search and build MozDef on top of the elastic search cluster? The second point: I don't know how resilient the MozDef frontends are. If it breaks down it will never arrive at the elastic search cluster. That being said, it is just my opinion. There might be counter arguments to both my points.

Lets compare the capabilities of MozDef with the capabilities given earlier:
1. MozDef stores its data in an elasticsearch cluster. It also accepts logs from multiple sources. The only downside is that it has to be in JSON format. But it is not a big problem because most shippers support json output. So MozDef accepts and stores data from multiple sources. This is a check for data aggregation.
2. According to their [docs](https://mozdef.readthedocs.io/en/latest/) it supports correlation by periodically running queries against the data. This is a check for both alerting and correlation since the alerts have to be programmed in python, giving you almost endless flexability. So a check for alerting and correlation.
3. Dashboards are also available in MozDef, moreover there are plenty of other Dashboarding tools like grafana or kibana which can be used. Kibana is enabled by default. So a check for dashboards.
4. Compliance I am not sure about. It is certainly not the focus of MozDef. I did not find any build in reporting functionality. You can do it using Kibana.
5. You can store the logs as long as you want. MozDef does not come with a pre shipped policy.
6. MozDef has built in support for investigations and incidents. 

So on the first look MozDef should be able to do most things a SIEM should do. You can also extend MozDef by writing plugins accoiding to documentation. ELK stack is also very integratable with other things.

## Installation and setup
I installed MozDef on a centos 7 VM. I choose to install using docker because that is the simplest way to do it.
Make sure that your VM has enough storage. 25GB should do the trick.
The following commands did the trick for me:

Install dependencies:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
yum -y install epel-release yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum update
yum -y install make gcc docker-ce git python-pip python-devel
pip install --upgrade pip
pip install docker-compose
# start docker
start docker
service docker start
# install MozDef
git clone https://github.com/mozilla/MozDef.git
cd MozDef
make build
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Make sure to install docker-ce! Because this version does have the --chown flag for the COPY command. If you install the docker package you will get the following error when installing it:


You can run MozDef by executing "make run".

You also need to add firewall rules to make sure that you can access the services from the browser:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[root@localhost MozDef]# firewall-cmd --add-service http --permanent
success
[root@localhost MozDef]# firewall-cmd --add-service https --permanent
success
[root@localhost MozDef]# firewall-cmd --add-port 9090/tcp --permanent
firewall-cmd --reload
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


I also installed flog[source] to generate some fake logs later, for testing.
I did execute the following commands:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
yum install go
go get -v -t -u github.com/mingrammer/flog
cd go/src/github.com/mingrammer/flog/
go build
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



## Inspection
The installation looks like this in docker:

I assume most of it setup okay. I am looking where the logging is of mozdef itself is stored. Any good SIEM should have some auditing trail. The nginx container has some logging, but it is very minimal. I found no audit logging in the elasticsearch container, but I assume this can be enabled. So it looks like you have to make some effort to make sure that all actions within the SIEM are auditable.


We create an account and then we get this screen when visiting the website. 

The interface is not particularly nice. The investigations and incident part seems very nice though. It is very intuitive and evidence based.

The following command is used to generate some logs: "flog -f json -n 1 | while read log; do curl -v --header "Content-Type: application/json" --request POST --data "$log" http://localhost:8080/events; done". This command generates 100 lines of fake apache logs in json format. An example log can be seen below:

You can inspect the logs in kibana, which is running on port 9090. It looks like this:



I tested the alerts by adding an IP address to the watchlist for which I already had an alert. And some time later I got indeed an alert as can be seen in the picture below:

You can click on mozdef to get more information:

There are buttons for escalating the alert, which is nice. If you escalate to an investigation you get something like this:

It looks pretty good. Lots of options for inserting evidence and hypotheses. 

Creating alerts is not easy. https://mozdef.readthedocs.io/en/latest/alert_development_guide.html describes the process. You have to program the alert in python. This does give you enormous flexability, correlation can be as advanced as you want, it does it at the cost of usability. You cannot expect that a SOC analyst can program alerts, so you need to have profesionals building these resources. On the other side, creating an alert also generates a file for unit tests, which more or less forces you to create tests for your alert. This is a good thing since it prevents misconfigurations. I only created an alert but did not push it to production.

I stopped inpsecting here. It looked like I had covered most functionality.

## Verdict
An adequate SIEM solution for small or medium companies, but not recommended for corporate environments. According to my demo setup it will take some effort to get it working. Below some pros and cons.

Pro's
1. Because MozDef uses Kibana it has excellent dashboarding capabilities.
2. Options for alert testing. This reduces human mistakes.
3. Simple and to the point.
4. Functionality for keeping track of incidents and investigations.
5. Alerts are very flexible.

Cons
1. Content creation is not straightforward. Programming skills necesarry.
2. No high-availability or clustering options. 
3. No user control or seperation of duties.
4. Documentation is minimal. 

Other SIEM's are doing way better in the usability aspect. Resource creation is usually plug and play or based on a simple query language. Other SIEM's do also have better support for important corporate functionality, like high availability, audit logging or integration with AD services. For example Splunk supports audit logging so all actions within Splunk are logged. 

What could be done to improve it? First of all make it more user friendly: update the GUI, add functionality for easy creation of alerts, no re-running make after every new alert. Secondly, implement role based access and log user actions. The last point would be to make MozDef cluster based. Maybe it is already possible, but I didn't read it in the documentation.

## Conclusion
It appears there are other opensource SIEM's. Some names which seemed promising are Wazuh and Apache Metron. Especially Wazuh seems interesting since it is also an ELK stack solution. In the future I will setup a wazuh installation and compare it with MozDef.

In my opinion MozDef could be a great tool when setting up a SIEM environment. The core functionality is pretty basic for a SIEM. This makes it pretty easy to use. Instead of learning how to operate a complex SIEM you can now create alerts to monitor on. MozDef is integrated with an incident management system so it covers the whole operation (observation (alert), investigation and incident). So you can use it as a SIEM, but don't expect that it suites you in every need. You might miss a lot of functionality.

To answer the question "Is it possible to build a fully opensource SIEM?". The answer is I don't know yet. Maybe MozDef can be used in the toolset, but it needs to be supplemented with other tools to get a top notch SIEM environment. In future posts I will research some other opensource SIEM tools. Next in line is Wazuh.

[1] https://mozdef.readthedocs.io/en/latest/introduction.html



