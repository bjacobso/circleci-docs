---
layout: classic-docs
title: "Administrator’s Overview"
category: [administration]
order: 1
description: "High-level overview of the CircleCI Installation process."
---
This article provides an overview of CircleCI installation, features, environments, and architecture in the following sections:

* TOC
{:toc}

CircleCI is a modern continuous integration and continuous delivery (CI/CD) platform. CircleCI is installable inside your private cloud or data center and is free to try for a limited time. CircleCI automates build, test, and deployment of software.

## Basic Features

After a software repository in GitHub or GitHub Enterprise is added as a project to the CircleCI application, every new commit triggers a build and notification of success or failure through webhooks with integrations for Slack, HipChat, Campfire, Flowdock, or IRC notifications. Code coverage results are available from the details page for any project for which a reporting library is added.

CircleCI may also be configured to deploy code to various environments, including the following:

- AWS CodeDeploy
- AWS EC2 Container Service (ECS)
- AWS S3
- Google Container Engine (GKE)
- Heroku

Other cloud service deployments can be easily scripted using SSH or by installing the API client of the service with your job configuration.

Contact us at <enterprise-support@circleci.com> to request a trial license.

## Build Environments

By default, CircleCI comes with a general-purpose image based on Ubuntu 14.04 (Trusty). It is possible to customize this image if necessary. 

## Architecture

CircleCI consists of two primary components: Services and Builders. Services run on a single instance that is comprised of the core application, storage, and networking functionality. Any number of Builder instances execute your jobs and communicate back to the Services. Both components must access your instance of GitHub or GitHub Enterprise on the network as illustrated in the following architecture diagram.

![A Diagram of the CircleCI Architecture]({{site.baseurl}}/assets/img/docs/ccie_arch.png)

### Services 

The machine on which the Service instance runs must not be restarted and may be backed up using built-in VM snapshotting. **Note:** It is possible to configure external data storage with PostgreSQL and Mongo for high availability and then use standard tooling for database backups. DNS resolution must point to the IP address of the machine on which the Services are installed. The following table describes the ports used for traffic on the Service instance:


| Source                      | Ports                   | Use                    |
|-----------------------------|-------------------------|------------------------|
| End Users                   | 80, 443                 | HTTP/HTTPS Traffic     |
| Administrators              | 22                      | SSH                    |
| Administrators              | 8800                    | Admin Console          |
| Builder Boxes               | all traffic / all ports | Internal Communication |
| GitHub (Enterprise or .com) | 80, 443                 | Incoming Webhooks      |
{: class="table table-striped"}

### Builders
The Builder instances run without storing state, enabling you to increase or decrease containers as needed. To ensure that there are enough Builder machines running to handle all of the builds, track the queued builds and increase the Builder instance machines as needed to balance the load.

Each machine on which the Builders are installed reserves two CPUs and 4GB of memory for coordinating builds. The remaining processors and memory create the containers. Larger machines are able to run more containers and are limited by the number of available cores after two are reserved for coordination. The following table describes the ports used on the Builder instances:


| Source                           | Ports                   | Use                                                            |
|----------------------------------|-------------------------|----------------------------------------------------------------|
| End Users                        | 64535-65535             | [SSH into builds feature](https://circleci.com/docs/2.0/ssh-access-jobs/) |
| Administrators                   | 80, 443                 | CircleCI API Access (graceful shutdown, etc)                   |
| Administrators                   | 22                      | SSH                                                            |
| Services Box                     | all traffic / all ports | Internal Communication                                         |
| Builder Boxes (including itself) | all traffic / all ports | Internal Communication                                         |
{: class="table table-striped"}

### GitHub
CircleCI uses GitHub or GitHub Enterprise credentials for
authentication which, in turn, may use LDAP, SAML, or SSH for access. That is, CircleCI will inherit the authentication  supported by your central SSO infrastructure. The following table describes the ports used on machines running GitHub to communicate with the Services and Builder instances.


| Source        | Ports   | Use          |
|---------------|---------|--------------|
| Services   | 22      | Git Access   |
| Services   | 80, 443 | API Access   |
| Builder  | 22      | Git Access   |
| Builder  | 80, 443 | API Access   |
{: class="table table-striped"}




