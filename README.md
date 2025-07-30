# Configuration for the E-commerce Microservices Platform

This repository contains the externalized configuration for the [E-commerce Platform Microservices](https://github.com/JayJay247in/E-commerce-platform) project.

## Purpose

This repository serves as the single source of truth for all application properties. The `config-server` service in the main application is pointed at this repository's URL. Services do not read property files from their own JARs; instead, they query the `config-server` on startup to fetch their configuration.

This approach allows for:
*   **Centralized Management:** All properties for all services and all environments are in one place.
*   **Dynamic Updates:** Changes committed to this repository can be reflected in the running services without requiring a rebuild or redeployment of the application.
*   **Security:** Sensitive information (like database passwords, API keys) can be stored here and kept out of the main application's source code. (Note: For production, a tool like HashiCorp Vault should be used for secrets).

## Structure

The configuration files follow a specific naming convention that the Spring Cloud Config Server understands.

*   `application.properties`: Contains global properties that are shared by **all** services. This is a good place for default logging levels, actuator settings, etc.
*   `{spring.application.name}.properties`: Contains properties specific to a single service. For example, `product-service.properties` contains configuration that only the `product-service` will load.
*   `{spring.application.name}-{profile}.properties`: (Optional) Contains properties for a specific service running in a specific Spring profile. For example, `order-service-prod.properties` would only be loaded by the `order-service` when it is started with the `prod` profile active.

## Usage

1.  The `config-server`'s `application.properties` file points to this repository's URL.
2.  Application services, on startup, contact the `config-server` and request their configuration based on their `spring.application.name` and any active profiles.
3.  To update a property, simply commit a change to the relevant file in this repository.
4.  The change can be applied to the running services by triggering a refresh, typically by sending a `POST` request to the `/actuator/refresh` endpoint of the target service(s).
