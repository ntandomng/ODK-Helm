	## Helm Chart for ODK Central

ODK Central Helm Chart
===========

Central is the [ODK](https://getodk.org/) server. It manages user accounts and permissions, stores form definitions, and allows data collection clients like ODK Collect to connect to it for form download and submission upload.

This repository is the helm chart for a kubernetes deployment of the Central project as a whole



Features
============
# Managing Users in Central
- Web User Roles
* Managing Web Users
* Creating a Web User
* Assigning a site-wide Web User Role
* Resetting a Web User password
* Retiring a Web User
- Managing App Users
* Creating an App User
* Configuring an App User mobile device
* Revoking an App User
# Managing Projects in Central
- Creating a Project
- Managing a Project
* Editing Project Settings
* Managing Project Roles
* Managing Project App Users
* Managing Form Access
* Archiving a Project
# Managing Forms in Central
# Managing Form Submissions in Central


Technical
==========

ODK Central uses a number of containers to run, most importantly:

- [Enketo](https://github.com/enketo/enketo-express) - ODK Central relies on Enketo for Webform functionality.
- [Pyxform](https://github.com/getodk/pyxform-http) - Central relies on pyxform-http to convert an XLSForm to an ODK XForm.
- [Nginx] - Central uses Nginx as a proxy ODK, which proxies both the Enketo and Service containers.
- [Postgres] - Central uses Postgresql for its datastore.
- [Redis] - Central uses the Redis in-memory data store as both a message broker and cache.


Installation
==========


This repository provides a method to install ODK Central into a kubernetes cluster as a helm chart. The minimum requirements as recommended by ODK is providing 1Gb of memory to the system. This said, the k8s deployments vary in resource reservations (both memory and CPU) and we recommend you tailor this to your need, however the Enketo Deployment and Pyxform Deployment must be given sufficient space otherwise the pods will fail to start.

Install the dependencies start the server:

```sh
git clone https://github.com/ntandomng/ODK-Helm.git
kubectl config get-contexts
helm install odk ODK-Helm
```

> IMPORTANT NOTE:
> Before applying the helm chart ensure that the enketo secret and api keys are unique to your deployment. 
> First Generate the secrets and keys (after cloning the repository) using the following commands:

```sh
sed -i "/^[[:space:]]*enketo-secret:/ s/:.*/: `LC_ALL=C tr -dc '[:alnum:]' < /dev/urandom | head -c64 `/" ./ODK-Helm/templates/secrets-configmap.yaml
sed -i "/^[[:space:]]*enketo-less-secret:/ s/:.*/: `LC_ALL=C tr -dc '[:alnum:]' < /dev/urandom | head -c32 `/" ./ODK-Helm/templates/secrets-configmap.yaml
sed -i "/^[[:space:]]*enketo-api-key:/ s/:.*/: `LC_ALL=C tr -dc '[:alnum:]' < /dev/urandom | head -c128 `/" ./ODK-Helm/templates/secrets-configmap.yaml
```

To confirm that the installation was succefful, use the following:

```sh
helm ls
kubectl get all -n odk
```

Once installation is confirmed, the first user must be created, using the following commands:

```sh
kubectl exec -i -t -n odk `kubectl get pods --no-headers -o custom-columns=":metadata.name" -n odk | grep service` -c service -- sh -c "clear; (bash || ash || sh)"
odk-cmd --email YOUREMAIL@ADDRESSHERE.com user-create
odk-cmd --email YOUREMAIL@ADDRESSHERE.com user-promote
```

Using the helm repository method:

```sh
#echo fill this in when the app is added on the helm repo
```


License
=======

All of ODK Central is licensed under the [Apache 2.0](https://raw.githubusercontent.com/getodk/central/master/LICENSE) License.
