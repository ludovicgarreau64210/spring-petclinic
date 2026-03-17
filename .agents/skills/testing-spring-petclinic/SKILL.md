# Testing Spring PetClinic

## Prerequisites
- JDK 21 must be installed (e.g. `sudo apt-get install openjdk-21-jdk`)
- Set `JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64` before running any build/run commands

## Build Verification

### Maven
```bash
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
./mvnw verify
```
Expect: `BUILD SUCCESS` with all tests passing (currently 59 tests).

### Gradle
```bash
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
./gradlew build
```
Expect: `BUILD SUCCESSFUL`.

### Checkstyle
Checkstyle runs as part of both Maven and Gradle builds. To run standalone:
```bash
./mvnw checkstyle:check
```
Expect: `0 Checkstyle violations`.

## Running the App Locally
```bash
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
./mvnw spring-boot:run
```
The app starts on `http://localhost:8080` using an in-memory H2 database with pre-loaded sample data.

## Key Verification Endpoints

### Java Version
- `GET /actuator/info` — check `build.java.source` and `build.java.target` fields
- Startup log shows `Starting PetClinicApplication using Java X.Y.Z`

### Virtual Threads
- When `spring.threads.virtual.enabled=true` is set in `application.properties`:
  - Server logs show thread names like `[tomcat-handler-N]` (virtual threads)
  - Without virtual threads, thread names show `[http-nio-8080-exec-N]` (platform threads)

### Actuator
- All actuator endpoints are exposed: `management.endpoints.web.exposure.include=*`
- `/actuator/info` — build info, git info, Java version
- `/actuator/configprops` — all config properties
- `/actuator/env` — environment details

## UI Smoke Test Pages
| Page | URL | What to verify |
|------|-----|----------------|
| Welcome | `/` | PetClinic branding, pet image |
| Find Owners | `/owners/find` | Search form renders |
| Owners List | `/owners?lastName=` | Table with owners from H2 (e.g. George Franklin) |
| Owner Detail | `/owners/1` | Name, address, city, phone, pets section |
| Veterinarians | `/vets.html` | Table with vets (e.g. James Carter) |
| Error | `/oups` | Custom error page |

## Tips
- The app uses Spring Boot DevTools, so it may restart automatically when classpath changes are detected during `spring-boot:run`.
- Chrome may aggressively autocomplete `localhost:8080` URLs from history. Use full URLs like `http://localhost:8080/owners/find` or navigate via the nav bar links to avoid this.
- The H2 database is in-memory and reloaded on each restart from `db/h2/schema.sql` and `db/h2/data.sql`.

## Devin Secrets Needed
No secrets are required for local testing. The app uses an embedded H2 database.
