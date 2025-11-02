# 🏥 HMS Patient Service

The **Patient Service** is a standalone Spring Boot microservice that manages patient records for the **Hospital Management System (HMS)**.  
It handles patient registration, updates, search, and deactivation, and exposes a full REST API with pagination and validation.

This service is containerized with Docker, uses MySQL as its datastore, and is built & published automatically to **GitHub Container Registry (GHCR)** through a CI pipeline.

---

## 🚀 Features

- CRUD operations for patient management
- Pagination support for listing
- Search by name or phone
- Soft delete (deactivation) for patients
- Validation for required fields (`email`, `phone`)
- Error handling with custom exceptions
- OpenAPI 3.0 (Swagger UI) documentation
- MySQL integration with Flyway migrations
- Dockerfile and Docker Compose for local setup
- GitHub Actions CI/CD workflow for automated builds and GHCR publishing

---

## 🧩 Tech Stack

| Layer | Technology |
|:------|:------------|
| Language | Java 17 |
| Framework | Spring Boot 3.x |
| ORM | Spring Data JPA |
| Database | MySQL 8 |
| Migrations | Flyway |
| Build Tool | Maven |
| API Docs | Springdoc OpenAPI (Swagger UI) |
| CI/CD | GitHub Actions |
| Containerization | Docker & GHCR |

---

## ⚙️ Local Setup

### 🧰 Prerequisites
- Java 17+
- Maven 3.6+
- Docker & Docker Compose

### 1️⃣ Build the project
```bash
mvn clean package
````

### 2️⃣ Run locally with Docker Compose

```bash
docker compose up --build
```

The service will start on **[http://localhost:8081](http://localhost:8081)**

### 3️⃣ Stop containers

```bash
docker compose down
```

---

## 🗃️ Database Configuration

| Property                        | Default Value                                |
| :------------------------------ |:---------------------------------------------|
| `spring.datasource.url`         | `jdbc:mysql://127.0.0.1:33061/patientdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC`                                           |
| `spring.datasource.username`    | `root`                                       |
| `spring.datasource.password`    | `example`                                    |
| `spring.jpa.hibernate.ddl-auto` | `update`                                     |

---

## 🧠 API Documentation

Swagger UI: [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html)
OpenAPI JSON: [http://localhost:8081/v3/api-docs](http://localhost:8081/v3/api-docs)

---

## 📘 Available APIs

### 1️⃣ Create Patient

**POST** `/v1/patients`

Creates a new patient record.

**Request Body:**

```json
{
  "firstName": "Kalyani",
  "lastName": "Muppidi",
  "email": "kalyani@example.com",
  "phone": "+919900112233",
  "dob": "1990-01-01"
}
```

**Responses:**

| Status | Description                                |
| :----- | :----------------------------------------- |
| 201    | Patient created successfully               |
| 400    | Missing required fields (`email`, `phone`) |

**Example:**

```bash
curl -X POST http://localhost:8081/v1/patients \
  -H "Content-Type: application/json" \
  -d '{"firstName":"Kalyani","lastName":"Muppidi","email":"kalyani@example.com","phone":"+919900112233","dob":"1990-01-01"}'
```

---

### 2️⃣ List Patients (Paginated)

**GET** `/v1/patients?page=0&size=10`

Returns a paginated list of patients.

**Response:**

```json
{
  "content": [
    {
      "patientId": 1,
      "firstName": "Kalyani",
      "lastName": "Muppidi",
      "email": "kalyani@example.com",
      "phone": "+919900112233",
      "active": true
    }
  ],
  "page": 0,
  "size": 10,
  "totalElements": 1,
  "totalPages": 1
}
```

---

### 3️⃣ Get Patient by ID

**GET** `/v1/patients/{id}`

Fetch a specific patient by ID.

**Responses:**

| Status | Description             |
| :----- | :---------------------- |
| 200    | Returns patient details |
| 404    | Patient not found       |

**Example:**

```bash
curl http://localhost:8081/v1/patients/1
```

---

### 4️⃣ Update Patient

**PUT** `/v1/patients/{id}`

Update existing patient details.

**Request Body:**

```json
{
  "firstName": "K",
  "lastName": "M",
  "email": "knew@example.com",
  "phone": "+919900112233"
}
```

**Responses:**

| Status | Description          |
| :----- | :------------------- |
| 200    | Updated successfully |
| 404    | Patient not found    |

**Example:**

```bash
curl -X PUT http://localhost:8081/v1/patients/1 \
  -H "Content-Type: application/json" \
  -d '{"firstName":"K","lastName":"M","email":"knew@example.com","phone":"+919900112233"}'
```

---

### 5️⃣ Delete (Deactivate) Patient

**DELETE** `/v1/patients/{id}`

Soft-deletes (deactivates) a patient.

**Responses:**

| Status | Description              |
| :----- | :----------------------- |
| 204    | Deactivated successfully |
| 404    | Patient not found        |

**Example:**

```bash
curl -X DELETE http://localhost:8081/v1/patients/1
```

---

### 6️⃣ Search Patients

**GET** `/v1/patients/search?name={name}`
**GET** `/v1/patients/search?phone={phone}`

Search patients by name or phone (supports pagination).

**Examples:**

```bash
curl "http://localhost:8081/v1/patients/search?name=Kalyani&page=0&size=5"
curl "http://localhost:8081/v1/patients/search?phone=%2B919900112233&page=0&size=5"
```

---

### 7️⃣ Check if Patient Exists

**GET** `/v1/patients/{id}/exists`

Checks if a patient exists and is active.

**Response:**

```json
{"exists": true, "active": true}
```

or

```json
{"exists": false}
```

---

## ⚠️ Error Handling

| Exception                   | Status | Description                                |
| :-------------------------- | :----- | :----------------------------------------- |
| `BadRequestException`       | 400    | Missing required fields (`email`, `phone`) |
| `ResourceNotFoundException` | 404    | Patient not found                          |
| `Exception`                 | 500    | Internal server error                      |

**Example Error Response:**

```json
{
  "code": "BAD_REQUEST",
  "message": "email and phone are required",
  "timestamp": "2025-11-02T07:15:45Z"
}
```

---

## 🧪 Testing

### Unit Tests

```bash
mvn test
```

### Manual Tests

Use **Swagger UI** or **Postman**:

* Swagger UI → [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html)
* OpenAPI JSON → [http://localhost:8081/v3/api-docs](http://localhost:8081/v3/api-docs)

---

## 🧰 CI/CD – GitHub Actions

This repository includes a CI pipeline that:

1. Builds with Maven
2. Runs tests
3. Builds and pushes a Docker image to GHCR

**Image naming convention:**

```
ghcr.io/<github-username>/<repository-name>:latest
```

To make your GHCR package public:

* Go to your GitHub profile → **Packages**
* Select the package → **Package Settings**
* Change visibility → **Public**

---

## 🗃️ Database Schema

| Column       | Type         | Description          |
| :----------- | :----------- | :------------------- |
| `patient_id` | BIGINT (PK)  | Primary key          |
| `first_name` | VARCHAR(100) | Patient’s first name |
| `last_name`  | VARCHAR(100) | Patient’s last name  |
| `email`      | VARCHAR(200) | Email address        |
| `phone`      | VARCHAR(50)  | Phone number         |
| `dob`        | DATE         | Date of birth        |
| `active`     | BOOLEAN      | Active flag          |
| `created_at` | TIMESTAMP    | Record creation time |

---

## 📦 Project Structure

```
src/main/java/org/hms/patient/
 ├── controller/
 │    └── PatientController.java
 ├── service/
 │    └── PatientService.java
 ├── repository/
 │    └── PatientRepository.java
 ├── model/
 │    └── Patient.java
 ├── dto/
 │    └── PaginationResponse.java
 ├── exception/
 │    ├── BadRequestException.java
 │    └── ResourceNotFoundException.java
 └── PatientServiceApplication.java

src/main/resources/
 ├── application.yml
 ├── db/migration/V1__init_patient.sql
 └── data.sql
```

---

## 🔗 Useful URLs

| Type         | URL                                                                                        |
| :----------- | :----------------------------------------------------------------------------------------- |
| Application  | [http://localhost:8081](http://localhost:8081)                                             |
| Swagger UI   | [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html) |
| OpenAPI JSON | [http://localhost:8081/v3/api-docs](http://localhost:8081/v3/api-docs)                     |
| Health Check | [http://localhost:8081/actuator/health](http://localhost:8081/actuator/health)             |

---

## 👥 Maintainers

**HMS Development Team**
*Developed as part of the Hospital Management System microservices project.*

---

✅ **Status:** Functional, containerized, and CI/CD ready for deployment to GHCR.


