# API Security Workshop

## Description

This Workshop showcase [Red Hat 3Scale API Management](https://www.redhat.com/en/technologies/jboss-middleware/3scale) features leaning towards API Management Security challenges.

The [Additional References](#additional-references) section will provide complementary assets for further reading covering additional details about related topics.

## Environment

- [Red Hat Openshift Container Platform 4.6](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
- [Red Hat 3Scale API Management 2.9](https://www.redhat.com/en/technologies/jboss-middleware/3scale)
- [Openshift Service Mesh 2.0.2](https://www.openshift.com/learn/topics/service-mesh)

## Agenda

0. [Openshift Setup](#deploy-openshift-setup)
1. [3Scale Setup](#deploy-3scale)

## Deployment

### 0 - Openshift Setup <a name="deploy-openshift-setup">

* Create a **3Scale project/namespace**. Example: `oc create namespace 3scale`

* Create a  *Kubernetes secret* to fetch images from **Red Hat´s Registry:**. Example: `oc -n 3scale create secret docker-registry threescale-registry-auth --docker-server=registry.redhat.io --docker-username="someuser" --docker-password=password`

* Deploy **3Scale Operator** onto **3Scale project/namespace**: `Operators > OperatorHub > Red Hat Integration - 3scale`

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator.png)

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator-install.png

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator-install-operator.png

  * don´t forget to select **3Scale project/namespace**

* Wait for the installation to finish. Before moving forward, we suggest double-checking the successfully deployment of **3Scale´s Operator**:

  ![3Scale Operator](images/deploy-openshift-setup/check-3scale-operator.png)

  ![3Scale Operator](images/deploy-openshift-setup/check-3scale-operator-namespace.png)

  * Via *api-resources:*

    ```
    oc api-resources | grep apimanagers
    apimanagers     apps.3scale.net     true      APIManager
    ```

### 1 - 3Scale Setup <a name="deploy-3scale">

* As stated on [3Scale Operator Guide](https://github.com/3scale/3scale-operator/blob/master/doc/operator-user-guide.md#prerequisites) the following requirements must be met:

  ```
  3 RWO (ReadWriteOnce) persistent volumes
  1 RWX (ReadWriteMany) persistent volume
  3scale's System component needs a RWX(ReadWriteMany) PersistentVolume for its FileStorage when System's FileStorage is configured to be a PVC (default behavior). System's FileStorage characteristics:
    Contains configuration files read by the System component at run-time
    Stores Static files (HTML, CSS, JS, etc) uploaded to System by its CMS feature, for the purpose of creating a Developer Portal
    System can be scaled horizontally with multiple pods uploading and reading said static files, hence the need for a RWX PersistentVolume when APIManager is configured to use PVC as System's FileStorage
  ```

    * When using *RHPDS* you need to deploy an additional *Storage Backend (NFS, OCS, etc)* or create a *PersistentVolumeClaim* in order to sucessfully deploy **3Scale**.

      ```
      oc create -f kubernetes/pvc-storage.yml -n 3Scale

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: system-storage
        labels:
          app: 3scale-api-management
          threescale_component: system
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: "100Mi"
        storageClassName: gp2
        volumeMode: Filesystem
      ```

    * if you want to customize **3Scale Password** then create a *Kubernetes Secret* with the following content:

      ```
      oc create -f kubernetes/seed.yml -n 3Scale

      apiVersion: v1
      kind: Secret
      metadata:
        name: system-seed
      stringData:
        MASTER_USER: admin
        MASTER_PASSWORD: redhat
        MASTER_ACCESS_TOKEN: redhat
        MASTER_DOMAIN: master
        ADMIN_USER: admin
        ADMIN_PASSWORD: redhat
        ADMIN_ACCESS_TOKEN: redhat
        TENANT_NAME: 3scale
      type: Opaque
      ```

* In order to deploy **#3scale**, navigate to `Operators > Installed Operators > API Manager > Create APIManager` and deploy the *API Manager*

  ![API Manager Deploy](images/deploy-3scale/deploy-3scale.png)

* If you prefer to use the command line, then create an *APIManager Object* as follows:

  ```
  oc create -f kubernetes/api-manager.yml -n 3Scale
  apiVersion: apps.3scale.net/v1alpha1
  kind: APIManager
  metadata:
   name: instance-3scale
  spec:
   wildcardDomain: $domain
  ```

  * don´t forget the *wildcardDomain* section with your **OpenShift Application domain**
  * when u
