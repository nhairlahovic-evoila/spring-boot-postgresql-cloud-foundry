# Spring Boot PostgreSQL Cloud Foundry Deployment

This repository demonstrates deploying a Spring Boot application integrated with PostgreSQL database to Cloud Foundry.

## Prerequisites
- **Cloud Foundry CLI**: Make sure you have the Cloud Foundry CLI installed.
- **Cloud Foundry Account**: You must have access to a Cloud Foundry environment.
- **Java 17**: Ensure that Java 17 is installed on your machine.

## Deployment Steps

### 1. Clone the Repository and Initialize Submodule

The application is packaged as a submodule under `/product-service`.
Clone the repository that contains the Spring Boot application and initialize the submodules:

```bash
git clone https://github.com/nhairlahovic-evoila/spring-boot-postgresql-cloud-foundry-demo.git
cd spring-boot-postgresql-cloud-foundry-demo
git submodule update --init
```

This will ensure that the `product-service` module is initialized and updated in the repository.

### 2. Build the Application

Navigate to the `product-service` directory and build the Spring Boot application using Maven:

```bash
cd product-service
./mvnw clean package
```

This will generate the JAR file `product-service-0.0.1-SNAPSHOT.jar` in the `target` directory.

### 3. Deploy to Cloud Foundry

Once the JAR file is built, navigate to the root directory where the `manifest.yml` file is located, and deploy the application to Cloud Foundry using the provided `manifest.yml`:
```bash
cf push
```

This command will:
- Deploy the `product-service-0.0.1-SNAPSHOT.jar`: The JAR file from the `product-service/target/` directory will be deployed.
- Use the configuration in `manifest.yml`: Cloud Foundry will automatically use the settings specified in the manifest for deployment.

### 4. Manifest explanation

#### Environment Variables (`env`)

- `JBP_CONFIG_OPEN_JDK_JRE`: This specifies the version of the Java Runtime Environment (JRE) to use. In this case, it sets the JRE to version 17 or higher.
- `SPRING_PROFILES_ACTIVE`: This activates the Spring `cloudfoundry` profile (environment-specific configurations in `application-cloudfoundry.properties`).
- `SERVICE_OFFERING_NAME`: This specifies the name of the Cloud Foundry service offering for PostgreSQL. The application will use this value to look for the service credentials in the `VCAP_SERVICES` environment variable at runtime.


#### Services (`services`)

`product-service-db`: This binds the application to a PostgreSQL database service in Cloud Foundry. The service must be created and available in your Cloud Foundry environment. 
During deployment, Cloud Foundry will inject the database credentials into the `VCAP_SERVICES` environment variable, which the application uses to connect to the database.
  ```yaml
  services:
  - product-service-db
  ```

**Note**: If the services section is omitted from the `manifest.yml`, the service binding can still be done manually after deployment with the following command:
```sh
cf bind-service product-service product-service-db
```

After binding, you will need to restart the application for the changes to take effect:

```sh
cf restart product-service
```

### 5. Access the Application

After a successful deployment, Cloud Foundry will provide a URL to access the application. You can use the following command to check the app's routes:

```bash
cf apps
```

The app will be accessible via the route `http://product-service.<your-cloud-foundry-domain>/`.


### 6. Managing the Application

Here are some useful commands for managing your deployed Spring Boot application on Cloud Foundry:

**1. Monitoring and Viewing Logs**

To monitor your application's logs, use the following command:
```bash
cf logs spring-app-demo --recent
```

This will display recent logs and help you debug or monitor the app’s performance.

**2. Cleaning Up**
To delete the application from Cloud Foundry, including any service bindings if they exist in the manifest, run:

```bash
cf delete spring-app-demo
```

This will stop and remove the application from your Cloud Foundry environment. If the application is bound to services (as defined in the `manifest.yml`), the service bindings will be removed, but the services themselves will remain active.
You may need to delete the services manually if they are no longer needed.


### 7. Testing the Application

After starting the application, you can use tools like Postman or cURL to interact with the API.

**1. Add a Product**

POST `http://product-service.<your-cloud-foundry-domain>/api/products`

Request Body:
```json
{
  "id": 1,
  "name": "Test Product Name",
  "description": "Test Product description",
  "price": 10.5
}
```

**2. Get All Products**

GET `/api/products`

Example cURL Command:

```bash
curl http://product-service.<your-cloud-foundry-domain>/api/products
```












