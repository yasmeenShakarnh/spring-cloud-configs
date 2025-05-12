# ☁️ spring-cloud-config

This repository contains **centralized configuration files** for all microservices in the LMS project using **Spring Cloud Config**.

## 📌 Purpose
The goal of this project is to externalize the configuration of different microservices, allowing them to fetch their settings from a centralized config server. This makes it easier to manage environment-specific settings and update them without needing to rebuild each service.

---

## 📂 Structure

Each `.properties` file in the root directory represents a configuration for a specific microservice:

| File                          | Description                                        |
|-------------------------------|----------------------------------------------------|
| `api-gateway-service.properties` | Configuration for API Gateway (routes, CORS, JWT) |
| `course-service.properties`      | PostgreSQL DB, Eureka client, JPA config           |
| `discovery-service.properties`   | Eureka Server setup                                |
| `security-service.properties`    | DB, JWT, Google OAuth2, and Swagger setup          |

---

## 🧠 Highlights

### ✅ `api-gateway-service.properties`
- Enables service discovery via Eureka.
- CORS and route predicates for:
  - `/auth/**` → `security-service`
  - `/courses/**` → `course-service`
- Adds response headers and JWT properties.

### 📚 `course-service.properties`
- Connects to PostgreSQL: `course_db`
- JPA config with `hibernate` dialect
- Eureka client settings
- Disables HATEOAS HAL JSON format

### 🧭 `discovery-service.properties`
- Configures Eureka discovery server
- Disables registration/fetch since it’s the main registry

### 🔐 `security-service.properties`
- Connects to PostgreSQL: `security_db`
- JWT properties (secret, token duration)
- Google OAuth2 client config (clientId, secret, URLs)
- Swagger UI settings for API documentation

---

## ⚙️ Usage Instructions

To use these configs:
1. Set up **Spring Cloud Config Server**.
2. Point your microservices to this Git repository using:
   ```properties
   spring.config.import=optional:configserver:http://localhost:8888
   ```
3. Make sure `spring.application.name` in each service matches the config filename prefix.

---

## 🛠️ Example Config Server `application.yml`

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/your-username/spring-cloud-configs
          searchPaths: .
  application:
    name: config-server
server:
  port: 8888
```

---

## 🛢️ Changing Database Configuration

To update the database settings for any microservice (such as `course-service` or `security-service`):

1. Open the relevant `.properties` file (e.g., `course-service.properties`).
2. Locate the following block:
   ```properties
   spring.datasource.url=jdbc:postgresql://localhost:5432/your_db_name
   spring.datasource.username=your_username
   spring.datasource.password=your_password
   ```
3. Replace the `url`, `username`, and `password` values with your own database credentials.

> 💡 Tip: For production environments, it is recommended to use environment variables or a secrets manager instead of hardcoding sensitive credentials.

### ✅ Example

If you're using a PostgreSQL container:

```properties
spring.datasource.url=jdbc:postgresql://db-container:5432/lms_prod
spring.datasource.username=postgres
spring.datasource.password=securePass123
```

Make sure your Docker or Kubernetes environment resolves `db-container` properly.

---

## 👩‍💻 Maintainer

This config repo is maintained by the LMS team. For updates or contributions, please submit a pull request or issue.
