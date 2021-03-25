# 3Scale First API

## Description

This module will cover how to create an **API Product, API Backend, Mapping Rules, Application Plan, Application** and promote then to *Staging* and *Production* environment.

## Deployment

### API Backend

* On your **3Scale Home Page** create a *Backend* with the following configuration, and click on *Create Backend* afterwards:

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/create-backend-helloworld.png)

  ```
  Name: hello-world-backend
  System name: hello-world-backend
  Private Base URL: https://echo-api.3scale.net/
  ```

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/deploy-backend-helloworld.png)

* Switch back to **3Scale Home Page** selecting *Dashboard*

  ![Deploy HelloWorld Backend](images/3scale_first_api/backend/dashboard-backend-helloworld.png)


## References

- [3Scale Backend](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html/glossary/threescale_glossary#backend)
