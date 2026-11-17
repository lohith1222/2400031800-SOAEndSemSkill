# UserRegistration

Spring Boot 3 / Java 17 REST API for registering users in PostgreSQL. Passwords are stored as BCrypt hashes.

## Requirements
- JDK 17
- Maven 3.6.3+ (or Eclipse's embedded Maven)
- PostgreSQL and pgAdmin 4

## 1. Create the database
In pgAdmin 4, connect to your local PostgreSQL server, right-click **Databases > Create > Database**, and name it `userdb`.

Or run this SQL while connected to the PostgreSQL server (not inside a database named `userdb`):

```sql
CREATE DATABASE userdb;
```

## 2. Set the database password
Open `src/main/resources/application.properties` and replace `CHANGE_ME` with the password for the PostgreSQL user `postgres`:

```properties
spring.datasource.password=YOUR_POSTGRES_PASSWORD
```

Do not include angle brackets. The database URL, username, and port are already set to `localhost:5432`, `postgres`, and `userdb`.

## 3. Build and run
From the project root:

```bash
mvn clean package
mvn spring-boot:run
```

Or run the packaged jar:

```bash
java -jar target/UserRegistration-0.0.1-SNAPSHOT.jar
```

## 4. Swagger UI
Open http://localhost:8080/swagger-ui/index.html and expand **POST /api/users/register**.

Request body:

```json
{
  "username": "john",
  "email": "john@gmail.com",
  "password": "John@123"
}
```

Success: HTTP `201 Created`, body:

```text
User registered successfully!
```

Duplicate email: HTTP `409 Conflict`, body:

```json
{"message":"Email already registered"}
```

Invalid fields return HTTP `400 Bad Request` with field-specific messages.

## 5. Verify in pgAdmin
Open Query Tool for `userdb` and run:

```sql
SELECT id, username, email, password
FROM users
ORDER BY id DESC;
```

The `password` column contains a BCrypt hash, not the original password. Never return this column from a public API.

## Notes
- Hibernate uses `ddl-auto=update`, so it creates the `users` table automatically when the application starts and updates the schema as entity fields change.
- Security permits the registration and Swagger routes for this demonstration. This is not a complete production authentication setup.
- The unique database constraint protects email uniqueness even if two registration requests arrive simultaneously.
