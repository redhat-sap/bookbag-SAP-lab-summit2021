# Intro

This workshop can be used to demonstrate the integration capabilities for Red Hat Integration portfolio with SAP's Digital Core.

# High-level architecture and components

The high-level architecture consists of 3 different RHEL 8.x servers:

* **bastion:** this is meant to be used as the jump host for SSH access to the environment and where to run SAP GUI from if required.
* **hana:** this is meant to be used as the RHEL server where to deploy SAP HANA.
* **s4hana:** this is meant to be used as the RHEL server where to deploy SAP S/4HANA.

And an OpenShift 4.x cluster with the following components deployed:

* **OpenShift Container Storage:** used for multi-purpose container storage backend.
* **Fuse Online:** used to demonstrate out of the box easy to implement integration capabilities with SAP S/4HANA and multi-purpose integrations.
* **3scale:** used to demonstrate API management capabilities for both SAP API Business Hub APIs and custom APIs deployed as microservices in OpenShift.
* **Backend Microservice:** used to demonstrate the integration with SAP S/4HANA using Red Hat Fuse.
* **Frontend Microservice:** used to present the data coming from SAP S/4HANA and SAP HANA to the user.

![Lab architecture](images/lab_architecture.png)

# Lab Access
This environment is provisioned using the Red Hat internal demo system. We at Red Hat embrace the use of IaC (Infrastructure as Code) for any lab/demo set up, that's why we have open-sourced the Framework (based in Ansible) we use for this. If you want to get more information on this topic, check the [GitHub](https://github.com/redhat-cop/agnosticd) repository we use to deploy these labs and demos.






The access details contain:

* OpenShift information including:
 * OpenShift web-console public URL
 * OpenShift web-console user
 * OpenShift web-console password
* 3scale information including:
 * 3scale namespace in OpenShift
 * 3scale API Management Portal user
 * 3scale API Management Portal password
* Fuse Online information including:
 * Fuse Online namespace in OpenShift
* Backend and Frontend information including:
 * Backend microservice Swagger public URL
 * Frontend microservice public URL

