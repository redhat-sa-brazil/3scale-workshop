# 3Scale First API

## Description

This module will cover how to create an **API Product, API Backend, Mapping Rules, Application Plan, Application** and promote then to *Staging* and *Production* environment.

## Deployment

0. [Hello World API - Backend](#deploy-helloworld-backend)
1. [Hello World API - Product](#deploy-helloworld-product)
2. [Hello World API - Mapping Rules](#deploy-helloworld-mappingrules)
3. [Hello World API - Application Plans](#deploy-helloworld-applicationplans)
4. [Hello World API - Application](#deploy-helloworld-application)
5. [Hello World API - Promote](#deploy-helloworld-promote)
6. [Hello World API - Test](#deploy-helloworld-test)

### 0. Hello World API - Backend <a name="deploy-helloworld-backend">

* On your **3Scale Home Page** create a *Backend* with the following configuration by clicking on *NEW BACKEND* button:

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/create-backend-helloworld.png)

* After configuring your **API Backend**, click on *Create Backend*

  ```
  Name: hello-world-backend
  System name: hello-world-backend
  Private Base URL: https://echo-api.3scale.net/
  ```

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/deploy-backend-helloworld.png)

* Switch back to **3Scale Home Page** by selecting *Dashboard*

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/dashboard-backend-helloworld.png)

### 1. Hello World API - Product <a name="deploy-helloworld-product">

* On your **3Scale Home Page** create a *Product* by clicking on *NEW PRODUCT* button:

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/create-product-helloworld.png)

* Create a *Product* with the following definition:

  ```
  Name: hello-world-product
  System name: hello-world-product
  ```

  ![Deploy HelloWorld Product](images/3scale_first_api/product/deploy-product-helloworld.png)

* On **hello-world-product** page, click on *Integration -> Configuration* and finally: *add a Backend and promote the configuration*:

  ![Deploy HelloWorld Product](images/3scale_first_api/product/integrate-product-backend.png)

* Select **hello-world-backend** from *Backend* menu and on the *Path* textbox, inform `/`

* Finally click on *Add to Product*

  ![Deploy HelloWorld Product](images/3scale_first_api/product/addbackend-product.png)

* Confirm that **hello-world-backend** is successfully created

  ![Deploy HelloWorld Product](images/3scale_first_api/product/addbackend-validate-product.png)

### 2. Hello World API - Mapping Rules <a name="deploy-helloworld-mappingrules">

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Mapping Rules](images/3scale_first_api/mappingrule/deploy-helloworld-mappingrules.png)

* Edit **hello-world-backend** configuration: *Backends -> hello world backend*

  ![Deploy HelloWorld Mapping Rules](images/3scale_first_api/mappingrule/select-backend-helloworld.png)

* Click on *Mapping Rules* on the left side menu

  ![Deploy HelloWorld Mapping Rules](images/3scale_first_api/mappingrule/mappingrules-helloworld.png)

* Click on *Add Mapping Rule* and add a Rule with the following parameters, and afterwards click on *Create Mapping Rule*

  ```
  Verb: GET
  Pattern: /hello
  Metric or method to increment: Hits
  Increment by: 1.
  Last?: blank
  Position: 1
  ```

  ![Deploy HelloWorld Mapping Rules](images/3scale_first_api/mappingrule/define-mappingrules-helloworld.png)

### 3. Hello World API - Application Plans <a name="deploy-helloworld-applicationplans">

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Application Plan](images/3scale_first_api/applicationplan/dashboard-helloworld-applicationplan.png)

* Switch to *Products* tab and click on *HELLO-WORLD-PRODUCT API*

  ![Deploy HelloWorld Application Plan](images/3scale_first_api/applicationplan/product-helloworld-applicationplan.png)

* Click on: *Overview -> Applications -> Application Plan*

  ![Deploy HelloWorld Application Plan](images/3scale_first_api/applicationplan/integration-helloworld-applicationplan.png)

* Click on *Create Application Plan* and define it using the following parameters:

  ```
  Name: hello-application-plan
  System name: hello-application-plan
  ```

  ![Deploy HelloWorld Application Plan](images/3scale_first_api/applicationplan/create-helloworld-applicationplan.png)

* Finally set **hello-application-plan** as the *Default Plan* and click on *Publish*

  ![Deploy HelloWorld Application Plan](images/3scale_first_api/applicationplan/publish-helloworld-applicationplan.png)

  * wait until **hello-application-plan State** is: *published*

### 4. Hello World API - Application <a name="deploy-helloworld-application">

* Switch to **Audience**

  ![Deploy HelloWorld Application](images/3scale_first_api/application/configure-helloworld-audience.png)

* Click on *Developer*

  ![Deploy HelloWorld Application](images/3scale_first_api/application/configure-helloworld-developer.png)

  ![Deploy HelloWorld Application](images/3scale_first_api/application/view-helloworld-developer.png)

* Click on *1 Application* top link and finally select *Create Application*

  ![Deploy HelloWorld Application](images/3scale_first_api/application/create-helloworld-application.png)

  ![Deploy HelloWorld Application](images/3scale_first_api/application/define-helloworld-application.png)

* Define an application with the following configuration and click on *Create Application*

  ```
  Application Plan: hello-application-plan
  Name: hello-world-app
  Description: hello-world-app
  ```

  ![Deploy HelloWorld Application](images/3scale_first_api/application/finish-helloworld-application.png)

  ![Deploy HelloWorld Application](images/3scale_first_api/application/view-helloworld-application.png)

### 5. Hello World API - Promote <a name="deploy-helloworld-promote">

* In the left menu, select: *Integration -> Configuration*

  ![Deploy HelloWorld Promote](images/3scale_first_api/promote/finish-helloworld-application.png)

* Promote the **API** by clicking on *Promote v.1 to Staging APIcast*

  ![Deploy HelloWorld Promote](images/3scale_first_api/promote/staging-helloworld-application.png)

    * take note of *Staging APIcast URL*

* Promote the **API** by clicking on *Promote v.1 to Production APIcast*

  ![Deploy HelloWorld Promote](images/3scale_first_api/promote/production-helloworld-application.png)

    * take note of *Production APIcast URL*

## References

- [3Scale Backend](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#backend)
- [3Scale Product](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#product)
- [3Scale Mapping Rule](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#mapping-rule)
- [3Scale Application Plan](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#plan)
- [3Scale Application](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#application)
