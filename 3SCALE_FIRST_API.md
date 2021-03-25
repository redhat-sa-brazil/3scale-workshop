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

* On your **3Scale Home Page** create a *Backend* with the following configuration by clicking on *New Backend* button:

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/create-backend-helloworld.png)

  ```
  Name: hello-world-backend
  System name: hello-world-backend
  Private Base URL: https://echo-api.3scale.net/
  ```

* After configuring your **API Backend**, click on *Create Backend*

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/deploy-backend-helloworld.png)

* Switch back to **3Scale Home Page** by selecting *Dashboard*

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/dashboard-backend-helloworld.png)

### 1. Hello World API - Product <a name="deploy-helloworld-product">

* On your **3Scale Home Page** create a *Product* with the following configuration, and click on *Create Product* afterwards:

  ![Deploy HelloWorld Product](images/deploy-helloworld-product/create-product-helloworld.png)

  ```
  Name: hello-world-product
  System name: hello-world-product
  ```

  ![Deploy HelloWorld Product](images/3scale_first_api/product/deploy-product-helloworld.png)

* On **hello-world-product** page, click on *Integration -> Configuration* and finally: *add a Backend and promote the configuration*:

  ![Deploy HelloWorld Product](images/3scale_first_api/product/integrate-product-backend.png)

* Select **hello-world-backend** from *Backend* menu and on the *Path* textbox, inform `/`

  ![Deploy HelloWorld Product](images/3scale_first_api/product/addbackend-product.png)

* Finally click on *Add to Product*

  ![Deploy HelloWorld Product](images/3scale_first_api/product/addbackend-validate-product.png)


## References

- [3Scale Backend](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#backend)
- [3Scale Product](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#product)
