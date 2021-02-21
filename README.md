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
2. [Hello World - Backend](#deploy-helloworld-backend)
3. [Hello World - Product](#deploy-helloworld-product)
4. [Hello World - Mapping Rules](#deploy-helloworld-mappingrules)
5. [Hello World - Application Plans](#deploy-helloworld-applicationplans)
6. [Hello World - Application](#deploy-helloworld-application)
7. [Hello World - Promote](#deploy-helloworld-promote)
8. [Hello World - Test](#deploy-helloworld-test)

## Deployment

### 0 - Openshift Setup <a name="deploy-openshift-setup">

* Create a **3Scale project/namespace**. Example: `oc create namespace 3scale`

* Create a  *Kubernetes secret* to fetch images from **Red Hat´s Registry**. Example: `oc -n 3scale create secret docker-registry threescale-registry-auth --docker-server=registry.redhat.io --docker-username="someuser" --docker-password=password`

* Deploy **3Scale Operator** onto **3Scale project/namespace**:
  `Operators > OperatorHub > Red Hat Integration - 3scale`

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator.png)

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator-install.png)

  ![3Scale Operator](images/deploy-openshift-setup/deploy-3scale-operator-install-operator.png)

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

      oc create -f kubernetes/pvc-storage.yml -n 3scale
      persistentvolumeclaim/system-storage created
      ```

    * if you want to customize **3Scale Password** then create a *Kubernetes Secret* with the following content:

      ```
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

      oc create -f kubernetes/seed.yml -n 3scale
      secret/system-seed created
      ```

* In order to deploy **3scale**, navigate to `Operators > Installed Operators > Red Hat Integration 3scale -> API Manager > Create APIManager` and deploy the *API Manager*

  ![API Manager Deploy](images/deploy-3scale/deploy-3scale.png)

  ![API Manager Deploy](images/deploy-3scale/deploy-3scale-apimanager.png)

* If you prefer to use the command line, then create an *APIManager Object* as follows:

  ```
  apiVersion: apps.3scale.net/v1alpha1
  kind: APIManager
  metadata:
   name: instance-3scale
  spec:
   wildcardDomain: $domain

  oc create -f kubernetes/api-manager.yml -n 3scale
  ```

  * don´t forget to update the *wildcardDomain* section with your **OpenShift Application domain**
  * when using *RHPDS* it´s recommended to disable *Kubernetes Resource Limits* and *Kubernetes Resource Requests* using [resourceRequirementsEnabled: false](https://github.com/3scale/3scale-operator/blob/master/doc/operator-user-guide.md#resources) property:

    ```
    apiVersion: apps.3scale.net/v1alpha1
    kind: APIManager
    metadata:
     name: instance-3scale
    spec:
     wildcardDomain: $domain
     resourceRequirementsEnabled: false

    oc create -f kubernetes/api-manager.yml -n 3scale
    ```

* Wait until all *pods* are successfully started. A similar output is expected:

  ```
  oc get pods -n 3scale

  NAME                               READY   STATUS      RESTARTS   AGE
  3scale-operator-5f59967f75-vvcdm   1/1     Running     0          80m
  apicast-production-1-29nvc         1/1     Running     0          3m39s
  apicast-production-1-deploy        0/1     Completed   0          4m29s
  apicast-staging-1-deploy           0/1     Completed   0          4m29s
  apicast-staging-1-hjvws            1/1     Running     0          3m39s
  backend-cron-1-deploy              0/1     Completed   0          5m18s
  backend-cron-1-lgbfc               1/1     Running     0          5m15s
  backend-listener-1-deploy          0/1     Completed   0          5m19s
  backend-listener-1-m8r4d           1/1     Running     0          5m15s
  backend-redis-1-5hsh9              1/1     Running     0          5m14s
  backend-redis-1-deploy             0/1     Completed   0          5m17s
  backend-worker-1-deploy            0/1     Completed   0          5m19s
  backend-worker-1-w26rn             1/1     Running     0          5m15s
  system-app-1-deploy                0/1     Completed   0          5m
  system-app-1-hook-post             0/1     Completed   0          71s
  system-app-1-hook-pre              0/1     Completed   0          4m58s
  system-app-1-z5ggv                 3/3     Running     0          2m35s
  system-memcache-1-9lxmj            1/1     Running     0          4m59s
  system-memcache-1-deploy           0/1     Completed   0          5m1s
  system-mysql-1-deploy              0/1     Completed   0          5m16s
  system-mysql-1-j4gzq               1/1     Running     0          5m13s
  system-redis-1-deploy              0/1     Completed   0          5m17s
  system-redis-1-dfmqv               1/1     Running     0          5m14s
  system-sidekiq-1-bvb6k             1/1     Running     0          4m53s
  system-sidekiq-1-deploy            0/1     Completed   0          5m1s
  system-sphinx-1-deploy             0/1     Completed   0          5m
  system-sphinx-1-vzt84              1/1     Running     0          4m58s
  zync-1-7zrx7                       1/1     Running     0          4m44s
  zync-1-deploy                      0/1     Completed   0          4m49s
  zync-database-1-deploy             0/1     Completed   0          4m48s
  zync-database-1-h79m6              1/1     Running     0          4m44s
  zync-que-1-deploy                  0/1     Completed   0          4m49s
  zync-que-1-fklk9                   1/1     Running     2          4m44s

  oc get routes -n 3scale | awk '{ print $1, $2 }'
  NAME HOST/PORT
  backend backend-3scale.apps.cluster-620c.620c.example.opentlc.com
  zync-3scale-api-jk8pl api-3scale-apicast-production.apps.cluster-620c.620c.example.opentlc.com
  zync-3scale-api-rgskr api-3scale-apicast-staging.apps.cluster-620c.620c.example.opentlc.com
  zync-3scale-master-wpct6 master.apps.cluster-620c.620c.example.opentlc.com
  zync-3scale-provider-9csj9 3scale-admin.apps.cluster-620c.620c.example.opentlc.com
  zync-3scale-provider-fr9bk 3scale.apps.cluster-620c.620c.example.opentlc.com

  oc get routes -n 3scale | grep 3scale-admin | awk '{ print $2 }'
  3scale-admin.apps.cluster-620c.620c.example.opentlc.com
  ```

* Try to access **3Scale Admin Portal (e.g https://3scale-admin.apps.cluster-620c.620c.example.opentlc.com/)** with the following credentials: `admin / redhat`

  ![API Manager Deploy](images/deploy-3scale/login-3scale.png)

  ![API Manager Deploy](images/deploy-3scale/home-3scale.png)

* If something goes wrong please refer to [Troubleshooting common 3scale installation issues](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html-single/installing_3scale/index#troubleshooting-common-threescale-installation-issues)

### 2 - Hello World API - Backend <a name="deploy-helloworld-backend">

* On your **3Scale Home Page** create a *Backend* with the following configuration, and click on *Create Backend* afterwards:

  ![Deploy HelloWorld Backend](images/deploy-helloworld-backend/create-backend-helloworld.png)

  ```
  Name: hello-world-backend
  System name: hello-world-backend
  Private Base URL: https://echo-api.3scale.net/
  ```

  ![Deploy HelloWorld Backend](images/deploy-helloworld-backend/deploy-backend-helloworld.png)

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Backend](images/deploy-helloworld-backend/dashboard-backend-helloworld.png)

* For additional information regarding **3Scale Backend** concept please refer to [3Scale Backend](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#backend)

### 3 - Hello World API - Product <a name="deploy-helloworld-product">

* On your **3Scale Home Page** create a *Product* with the following configuration, and click on *Create Product* afterwards:

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/create-product-helloworld.png)

  ```
  Name: hello-world-product
  System name: hello-world-product
  ```

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/deploy-product-helloworld.png)

* On **hello-world-product** page, click on *Integration -> Configuration* and finally: *add a Backend and promote the configuration*:

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/integrate-product-backend.png)

* Select **hello-world-backend** from *Backend* menu and on the *Path* textbox, inform `/`

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/addbackend-product.png)

* Finally click on *Add to Product*

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/addbackend-validate-product.png)

* For additional information regarding **3Scale Product** concept please refer to [3Scale Product](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#product)

### 4 - Hello World API - Mapping Rules <a name="deploy-helloworld-mappingrules">

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Mapping Rules](images/deploy-helloworld-mappingrules/deploy-helloworld-mappingrules.png)

* Edit **hello-world-backend** configuration: *Backends -> hello world backend*

  ![Deploy HelloWorld Mapping Rules](images/deploy-helloworld-mappingrules/select-backend-helloworld.png)

* Click on *Mapping Rules* on the left side menu

  ![Deploy HelloWorld Mapping Rules](images/deploy-helloworld-mappingrules/mappingrules-helloworld.png)

* Click on *Add Mapping Rule* and add a Rule with the following parameters, and afterwards click on *Create Mapping Rule*

  ```
  Verb: GET
  Pattern: /hello
  Metric or method to increment: Hits
  Increment by: 1.
  Last?: blank
  Position: 1
  ```

  ![Deploy HelloWorld Mapping Rules](images/deploy-helloworld-mappingrules/define-mappingrules-helloworld.png)

### 5. Hello World API - Application Plans <a name="deploy-helloworld-applicationplans">

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Application Plan](images/deploy-helloworld-applicationplans/dashboard-helloworld-applicationplan.png)

* Switch to *Products* tab and click on *HELLO-WORLD-PRODUCT API*

  ![Deploy HelloWorld Application Plan](images/deploy-helloworld-applicationplans/product-helloworld-applicationplan.png)

* Click on: *Overview -> Applications -> Application Plan*

  ![Deploy HelloWorld Application Plan](images/deploy-helloworld-applicationplans/integration-helloworld-applicationplan.png)

* Click on *Create Application Plan* and define it using the following parameters:

  ```
  Name: hello-application-plan
  System name: hello-application-plan
  ```

  ![Deploy HelloWorld Application Plan](images/deploy-helloworld-applicationplans/create-helloworld-applicationplan.png)

* Finally set **hello-application-plan** as the *Default Plan* and click on *Publish*

  ![Deploy HelloWorld Application Plan](images/deploy-helloworld-applicationplans/publish-helloworld-applicationplan.png)

  * wait until **hello-application-plan State** is: *published*

### 6. Hello World API - Application <a name="deploy-helloworld-application">

* Switch to **Audience**

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/configure-helloworld-audience.png)

* Click on *Developer*

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/configure-helloworld-developer.png)

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/view-helloworld-developer.png)

* Click on *1 Application* top link and finally select *Create Application*

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/create-helloworld-application.png)

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/define-helloworld-application.png)

* Define an application with the following configuration and click on *Create Application*

  ```
  Application Plan: hello-application-plan
  Name: hello-world-app
  Description: hello-world-app
  ```

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/finish-helloworld-application.png)

  ![Deploy HelloWorld Application](images/deploy-helloworld-application/view-helloworld-application.png)

### 7. Hello World API - Promote <a name="deploy-helloworld-promote">

* In the left menu, select: *Integration -> Configuration*

  ![Deploy HelloWorld Promote](images/deploy-helloworld-promote/finish-helloworld-application.png)

* Promote the **API** by clicking on *Promote v.1 to Staging APIcast*

  ![Deploy HelloWorld Promote](images/deploy-helloworld-promote/staging-helloworld-application.png)

    * take note of *Staging APIcast URL*

* Promote the **API** by clicking on *Promote v.1 to Production APIcast*

  ![Deploy HelloWorld Promote](images/deploy-helloworld-promote/production-helloworld-application.png)

    * take note of *Production APIcast URL*

### 8. Hello World API - Test <a name="deploy-helloworld-test">

* In order to test the **Staging APICast**, just execute a curl on the generated URL:

  ```
  curl "https://hello-world-produc-3scale-apicast-staging.apps.cluster-51e3.51e3.example.opentlc.com:443/?user_key=<yourkey>"

  {
  "method": "GET",
  "path": "/",
  "args": "user_key=3103a2e304a1fa4541c8efc34ab0d93d",
  "body": "",
  "headers": {
    "HTTP_VERSION": "HTTP/1.1",
    "HTTP_HOST": "echo-api.3scale.net",
    "HTTP_ACCEPT": "*/*",
    "HTTP_USER_AGENT": "curl/7.64.1",
    "HTTP_X_3SCALE_PROXY_SECRET_TOKEN": "Shared_secret_sent_from_proxy_to_API_backend_77ad267d49292f55",
    "HTTP_X_REAL_IP": "10.128.2.23",
    "HTTP_X_FORWARDED_FOR": "187.2.139.101, 52.22.150.143, 10.0.101.74",
    "HTTP_X_FORWARDED_HOST": "echo-api.3scale.net",
    "HTTP_X_FORWARDED_PORT": "443",
    "HTTP_X_FORWARDED_PROTO": "https",
    "HTTP_FORWARDED": "for=10.0.101.74;host=echo-api.3scale.net;proto=https"
  },
  "uuid": "3109ab09-4bba-49f6-b034-bc078ed1d923"
  }%
  ```

  ![Deploy HelloWorld Test](images/deploy-helloworld-test/staging-helloworld-test.png)

* In order to test the **Production APICast**, just execute a curl on the generated URL:

  ```
  curl https://hello-world-produc-3scale-apicast-production.apps.cluster-51e3.51e3.example.opentlc.com:443/\?user_key\=$user_key
  {
    "method": "GET",
    "path": "/",
    "args": "user_key=3103a2e304a1fa4541c8efc34ab0d93d",
    "body": "",
    "headers": {
      "HTTP_VERSION": "HTTP/1.1",
      "HTTP_HOST": "echo-api.3scale.net",
      "HTTP_ACCEPT": "*/*",
      "HTTP_USER_AGENT": "curl/7.64.1",
      "HTTP_X_3SCALE_PROXY_SECRET_TOKEN": "Shared_secret_sent_from_proxy_to_API_backend_77ad267d49292f55",
      "HTTP_X_REAL_IP": "10.131.0.17",
      "HTTP_X_FORWARDED_FOR": "187.2.139.101, 50.16.42.192, 10.0.101.74",
      "HTTP_X_FORWARDED_HOST": "echo-api.3scale.net",
      "HTTP_X_FORWARDED_PORT": "443",
      "HTTP_X_FORWARDED_PROTO": "https",
      "HTTP_FORWARDED": "for=10.0.101.74;host=echo-api.3scale.net;proto=https"
    },
    "uuid": "ca1d231b-5b7b-4f32-b246-a338de792f5c"
  }
  ```

  ![Deploy HelloWorld Test](images/deploy-helloworld-test/prod-helloworld-test.png)

## Additional References <a name="additional-references">
