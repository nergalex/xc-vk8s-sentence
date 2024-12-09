Publish Sentence app in vK8S
####################################################################

.. raw:: html

    <a href="http://www.youtube.com/watch?v=Th6kKmvHxtI"><img src="http://img.youtube.com/vi/Th6kKmvHxtI/0.jpg" width="800" height="600" title="demo vK8S" alt="demo vK8S"></a>


.. contents:: Contents
    :local:

F5 Distributed Cloud (XC) is a ridiculously easy platform to host and publish secure containerized applications.
This repo is a simple example on how to do that in few API calls.


App design
*****************************************

``Sentence`` is micro-services based application, composed of the following services:
    - 4 x ``word`` type (adjective, location, animal, color)
    - ``generator``
    - ``frontend``
    - ``background``

The goal of this app is to generate a sentence:
    - each ``word`` returns a random word
    - ``generator`` makes one call to each ``word`` service
    - ``frontend`` calls ``generator`` and returns the full sentence in a Web page
    - ``background`` return a random background to the browser

XC vK8S cluster
*****************************************
Through the XC Console (GUI or API), your virtual Kubernetes (vK8S) cluster is deployed on XC nodes,
named Customer Edges (CE).
For example: 1 Customer Edge of 1 node in AWS

.. image:: ./_pictures/XC-CE.png
   :align: center
   :width: 700
   :alt: vK8S

XC Workloads
*****************************************
Once your App component images are built and uploaded to your Private Registry,
app workloads are deployed in your vK8S.

.. image:: ./_pictures/XC-vK8S-workloads.png
   :align: center
   :width: 1200
   :alt: workloads

Each workloads publish an internal vK8S service

.. image:: ./_pictures/XC-vK8S-service.png
   :align: center
   :width: 1200
   :alt: service

For troubleshooting, the workload can be accessed through a shell via the XC console

.. image:: ./_pictures/XC-vK8S-vK8S-UI-sh.png
   :align: center
   :width: 1200
   :alt: shell

or you can use your own IDE by uploading your vK8S Kubeconfig file

.. image:: ./_pictures/XC-vK8S-Lens.png
   :align: center
   :width: 1200
   :alt: Lens

XC internal API GW
*****************************************

A XC Load-Balancer allows you to manage the publication of your internal APIs and route them to the related service

.. image:: ./_pictures/XC-LB-route.png
   :align: center
   :width: 1000
   :alt: Internal API - LB

The access logs or request logs are available in the XC Console, with a high level of details

.. image:: ./_pictures/XC-LB-log.png
   :align: center
   :width: 1000
   :alt: Internal API - log

XC public exposition
*****************************************

A XC Load-Balancer allows you to manage the publication of your frontend Web server,
TLS certificate and DNS managed by F5,
and with a security policy.

.. image:: ./_pictures/XC-LB-public.png
   :align: center
   :width: 1000
   :alt: Public Web - LB

The security logs are available in the XC Console, with an AI assistant to help you to explain the cause and the next steps to do

.. image:: ./XC-LB-log_security.png
   :align: center
   :width: 1000
   :alt: Public Web - log

Automation
*****************************************
The ``playbooks`` directory contains the Ansible role used to deploys the Sentence app

.. code-block:: yaml

        extra_app:
          components:
            - name: generator
              location: /api/sentence
              repo: 'https://gitlab.com/sentence-app/generator.git'
              version: development
              env:
                - name: NAMESPACE
                  value: demo
                - name: PREFIX
                  value: ms
            - name: frontend
              location: /
              repo: 'https://gitlab.com/sentence-app/frontend.git'
              version: main
            - name: adjectives
              location: /adjectives
              repo: 'https://gitlab.com/sentence-app/adjectives.git'
              version: main
            - name: animals
              location: /animals
              repo: 'https://gitlab.com/sentence-app/animals.git'
              version: main
            - name: backgrounds
              location: /backgrounds
              repo: 'https://gitlab.com/sentence-app/backgrounds.git'
              version: main
            - name: colors
              location: /colors
              repo: 'https://gitlab.com/sentence-app/colors.git'
              version: main
            - name: locations
              location: /locations
              repo: 'https://gitlab.com/sentence-app/locations.git'
              version: main
          domain: f5xcdev.com
          name: sentence
        extra_jumphost:
          name: jumphost
        extra_namespace: demo
        extra_owner_email: my-emain@gg.com
        extra_volterra:
          tenant:
            full: my-xc-tenant-name-long
            short: my-xc-tenant-name-short
          token: my-xc-tenant-token
        extra_volterra_site_id: 3







