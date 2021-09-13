# Exercise 5: SAP GitOps and CI/CD

Using Red Hat OpenShift GitOps and Red Hat OpenShift Pipelines to implement GitOps and CI/CD for the side-by-side backend and frontend demo applications.

# Intro

This workshop can be used to demonstrate the automation capabilities while building and deploying cloud-native microservices in OpenShift that integrates with SAP.

![Infra layout](images/infra_layout.png)

# Steps 

All the required files to deploy and configure Argo CD and OpenShift Pipelines are included on [this repo](https://github.com/rickgcv/sap-side-by-side-ci). The very first thing you must do is to fork and clone the repo as you will need to commit some changes into your own fork to check all the features in action.

First log on to your OpenShift Cluster using the `oc` command provided in the login details and change to the project `openshift-gitops`.

```bash
oc project openshift-gitops
```

All the Tekton pipelines and required objects will be managed by Argo CD and these are part of the Argo CD Applications deployed by the instructions below. Different Tekton Pipelines will be automatically configured for dev and qa branches of the following repositories which contain the application code:

- [Backend Microservice](https://github.com/redhat-sap/sap-side-by-side-be)
- [Frontend Microservice](https://github.com/redhat-sap/sap-side-by-side-fe)

To test all the capabilities included on this demo you will need to fork and clone both repositories as you will need to configure different Hooks in your forked repositories so you can test how changes on the `dev` or `qa` branches of those repositories go from there to a running microservice in OpenShift with your code.

The following files must be changed to point to your own repositories:

- app-fe-cicd/dev-webhook.yml
- app-fe-cicd/pipeline-resources.yml
- app-fe-cicd/qa-webhook.yml
- app-be-cicd/dev-webhook.yml
- app-be-cicd/pipeline-resources.yml
- app-be-cicd/qa-webhook.yml

Also you will need to change the following files to match your own repo for Argo CD so you can test changes in your configuration and see how Argo CD automatically syncs the information from GitHub into OpenShift:

- argocd/argo-app-be-qa.yml
- argocd/argo-app-be-cicd.yml
- argocd/argo-app-be-dev.yml
- argocd/argo-app-fe-dev.yml
- argocd/argo-app-fe-qa.yml
- argocd/argo-app-fe-cicd.yml

### Check the needed OpenShift operators are installed

The additional OpenShift operators needed for this exercise are the `Red Hat OpenShift Pipelines` (for Tekton) and the `Red Hat OpenShift GitOps` operator (for Argo CD). They are already installed in the OCP cluster when it is deployed, you can ckeck it in the Openshift Console:

![Pipelines operator](images/pipeline_operator.gif)



### Create an Argo CD Deployment

Go to your clone of the `sap-side-by-side-ci` repo and from the parent directory run the following commands:

```bash
oc create -f argocd/argocd.yml -n argocd
oc create -f argocd/cluster-role-bind.yml
```

### Create new Applications in Argo CD

From the same directory of your cloned repo run:

```bash
oc create -f argocd/argo-app-be-dev.yml
oc create -f argocd/argo-app-be-qa.yml
oc create -f argocd/argo-app-fe-dev.yml
oc create -f argocd/argo-app-fe-qa.yml
oc create -f argocd/argo-app-be-cicd.yml
oc create -f argocd/argo-app-fe-cicd.yml
```

### Get Argo CD credentials for `admin` user

```
oc get secret sap-argocd-cluster -o jsonpath='{.data.admin\.password}' | base64 -d
```

You can also obtain the credentials from the OpenShift console and:

![ArgoCD credentials](images/argocd_credentials.gif)

### Check results

Use the credential for Argo CD `admin` user to login into Argo webconsole and you should see something similar to this, whith all the applications managed by Argo CD started to sync their objects into OpenShift.

![Argo Applications](images/argo01.png)

If you click on any of the Applications you can see more details on the objects that have been synced already and the actual status.


![Argo Application details](images/argo02.png)

If you check the existing Pipelines on any of the `cicd `Projects that have been created in OpenShift you will see the Pipeline status and the results from the first PipelineRun once you have committed any code to your GitHub `dev` or `qa` branches. This will not happen until you follow the last step explained below configuring your GitHub hooks to notify the EventListeners configured for your Pipelines.

![Pipeline](images/tekton01.png)

### Configure your GitHub hook

On each GitHub repository you need to configure 2 different hooks. Use the following command to get the endpoint information for each one:

- Frontend microservices hook endpoitns

    ```bash
     oc get routes -n app-fe-cicd fe-pipeline-webhook-dev -o jsonpath='{.spec.host}'
     oc get routes -n app-fe-cicd fe-pipeline-webhook-qa -o jsonpath='{.spec.host}'
    ```

- Backend microservices hook endpoitns

    ```bash
     oc get routes -n app-be-cicd be-pipeline-webhook-dev -o jsonpath='{.spec.host}'
     oc get routes -n app-be-cicd be-pipeline-webhook-qa -o jsonpath='{.spec.host}'
     ```

You can also get the routes from the OpenShift console:

![Routes](images/routes.gif)

Now go to your cloned repositories `sap-side-by-side-fe` and `sap-side-by-side-be` and add the routes you have obtained. in both you can see that there is a `dev` and a `qa` branch so we need one webhook per branch.

### Make a modification in your repositories

In order to check how the Tekton pipelines are created and they apply the modifications that we make in our repositories directly to the applications that we have deployed in our OpenShift cluster we will go to our cloned GitHub repositories `sap-side-by-side-fe` and `sap-side-by-side-be` and modify one of the files, for example `README.md` in both the `dev` and `qa` branches so that we can see in the OpenShift console how the respective pipelines in the correspondent CI/CD projects (`app-be-cicd` and `app-fe-cicd`) are run.

![Pipeline run](images/run_pipeline.gif)