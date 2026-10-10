# Project: Design & Implementation of a Smart Water Management Software Backend System

## Case Study: Rostov-on-Don
This is the official repository for the M.Sc Final Qualification Project, developed within the Department of Applied Mathematics & Informatics at Southern Federal University.

---

## 1. Frontend Part

### 1. Who will use the frontend?
The frontend client applications will be used by two main target audiences in Rostov-on-Don: Citizens (the general public looking for water availability and safety data) and Administrative Authorities (municipal operators and environmental water inspectors managing infrastructure records).

### 2. What is the main goal of the frontend?
The primary goal is to provide a clean, highly accessible user interface that delivers real-time transparency regarding local water scheduling, municipal distribution statuses, and calculated localized Water Quality Index (WQI) public safety classifications.

### 3. What screens or pages are needed?
The responsive interface requires the following main functional views:
* Public Live Map View (showing water booths, local distribution statuses, and geographic safety zones)
* Water Quality Trends & Metrics Dashboard (displaying historical charts of pH and contaminant trace changes)
* Citizen Issues & Reporting Portal (for registering broken water booths or supply outages)
* Administrative Authentication Panel
* Inspector Data Entry Console (for uploading recent laboratory sampling values)
* System Configurations & User Management Panel

### 4. How does the user move between screens?
Navigation utilizes a client-side single-page router. Public users navigate via a persistent top header bar and an interactive map overlay, while administrative users transition through a role-guarded vertical navigation panel accessible only upon successful login.

### 5. What data is shown on each screen?
* Public Map View: Geolocation coordinates of water booths, operation status flags (Active, Maintenance, Unavailable), and color-coded safety indicators.
* Quality Dashboard: Current pH indicators, exact contamination values, sampling dates, and an aggregate textual safety rating (Safe, Moderate, Unsafe).
* Entry Console: Form fields displaying raw numerical fields waiting for validation and save verification.

### 6. What forms and input fields are needed?
* Public Reporting Form: Dropdown selector for issue type (e.g., Seasonal Fluctuations, Booth Outage), text input for location coordinates, and a rich text field for situational context.
* Laboratory Data Form: Numeric floating-point inputs for raw pH values, parts-per-million (ppm) contamination levels, and a calendar date-picker for the precise test sampling date.

### 7. What rules check the user input?
The interface implements strict validation checks:
* Enforcing numerical bounds on pH variables (strictly capping values between 0.0 and 14.0).
* Preventing the selection of future dates for past water safety test samplings.
* Mandating character boundaries on text descriptions (minimum 10 characters, maximum 500 characters).

### 8. What error messages are shown to the user?
Users see descriptive, actionable messages: Inline warning text like "Please input a valid pH between 0.0 and 14.0" under incorrect inputs, or prominent top-center alert alerts like "Network Connection Lost: Unable to pull current Rostov-on-Don grid metrics."

### 9. What happens while data is loading, empty, or successful?
* Loading: The interface uses structural skeleton loaders mimicking the layout of charts and maps to establish visual continuity.
* Empty: The screen displays clear informational text: "No water reports submitted for this sector within the selected timeframe."
* Success: Visual components fill smoothly, and a self-clearing toast confirms actions: "Laboratory report successfully submitted."

### 10. How should the layout change for phones, tablets, and computers?
The interface relies on fluid layout boundaries:
* Computers: Dual-pane layout displaying an active map visualization side-by-side with an analytical data stream panel.
* Tablets: The data stream panel transitions into a collapsible slide-out drawer positioned over the base map canvas.
* Phones: Single-pane focus that stacks components vertically and switches tables into touch-friendly cards.

### 11. What colors, fonts, and style rules are used?
The visual identity utilizes theme colors inspired by environmental utility applications: Water Clean Blue (#0284C7) for structural components, Safety Green (#16A34A) for Safe designations, Alert Amber (#D97706) for Moderate zones, and Danger Red (#DC2626) for Unsafe warnings, using the clean sans-serif Inter font family.

### 12. What languages and themes are needed?
The application provides runtime language localization packages for both Russian and English to serve the diverse metropolitan population of Rostov-on-Don. It includes high-contrast viewing toggles alongside standard Light and Dark modes to ensure visibility under direct sunlight during summer field inspections.

### 13. Which browsers and versions must be supported?
The client targets modern web platform engines: Google Chrome (v115+), Mozilla Firefox (v116+), Apple Safari (v16+), and Microsoft Edge (v115+), completely discarding support for deprecated internet environments to maintain optimal cryptographic standards.

### 14. How does the frontend connect to the backend?
The system executes asynchronous HTTP network communications over a secure TLS channel to the Spring Boot REST API layer. Administrative requests append an encrypted Bearer JSON Web Token (JWT) payload within the request headers to pass underlying server authentication checks.

### 15. How will the frontend be tested and checked for speed?
The validation loop combines component-level unit assertions via Jest, functional end-to-end route testing via Cypress, and strict asset optimization testing using Google Lighthouse, targeting a First Contentful Paint (FCP) metric of less than 1.5 seconds.

---

## 2. Backend Part

### 1. What is the main goal of the backend?
The primary goal of the backend application is to serve as a robust, centralized, and highly secure computational engine built on Spring Boot that exposes RESTful APIs to process water distribution workflows, track laboratory quality logs, and handle role-based platform access.

### 2. Which user roles exist?
The security framework defines two explicit enterprise access levels mapping to municipal roles:
* `ROLE_CITIZEN` (Anonymous or verified public viewers)
* `ROLE_ADMINISTRATOR` (Municipal system operators, laboratory technicians, and regional water inspectors)

### 3. What actions can each role do?
* `ROLE_CITIZEN`: Execute read actions on public availability channels, review safety indicators, look up historical water trends, and submit local booth outage tickets.
* `ROLE_ADMINISTRATOR`: Perform full CRUD capabilities across all system modules, update master distribution maps, overwrite schedules, and publish lab metrics.

### 4. What business rules must be followed?
Core computational business rules include:
* Whenever fresh sample parameters are uploaded, the backend must instantly execute the mathematical Water Quality Index (WQI) formula to assign the safety classification.
* If a region's WQI crosses into the Unsafe boundary, the system must trigger automated warning hooks to alert connected channels.
* Outage tickets cannot be set to closed without assigning a resolution category tag.

### 5. What actions does the backend provide to the frontend and mobile app?
The RESTful architecture exposes clear, structured communication boundaries:
* `/api/v1/auth/login` (Processes security authorization keys)
* `/api/v1/water-quality/samples` (Manages laboratory testing records)
* `/api/v1/distribution/schedules` (Exposes regional availability times)
* `/api/v1/incidents/reports` (Tracks citizen-reported local grid failures)

### 6. What input does each action need?
POST mutations expect explicitly typed JSON bodies. For example, creating a water quality log requires a payload with a valid UUID `boothId`, a numeric `phValue` (0.0–14.0), a numeric `contaminationPpm`, a string-formatted ISO-8601 `testDate`, and a list object containing raw parameter ratings.

### 7. What output does each action return?
All responses use a standardized JSON wrapper containing a boolean `success` flag, an ISO-8601 unified `timestamp`, a nullable nested `data` object returning requested information, and an `error` structure detailing validation faults when a request fails.

### 8. How is the input checked?
Verification runs on a two-tier pipeline. Inbound requests are validated at the controller edge via Spring Validation decorators (such as `@NotNull`, `@Min`, `@Max`). If structural checks pass, the payload enters the Spring Boot service layer, where business rules are evaluated against active database states.

### 9. How do login and user verification work?
Authentication leverages Spring Security and stateless JWT mechanics. Administrators post credentials to the authentication router. The backend verifies hashes against stored database entities using BCrypt, generates an encapsulated, signed JWT access key, and returns it to the client.

### 10. How are access rights checked?
Authorization is evaluated using Method Security annotations (such as `@PreAuthorize("hasRole('ADMINISTRATOR')")`) bound across the Spring service and controller interfaces. The security context extracts claims from the parsed incoming JWT token to check permissions before allowing any operation.

### 11. What error codes and messages are returned?
The REST endpoints communicate failure states using explicit HTTP status mappings:
* `400 Bad Request` coupled with `ERR_INVALID_SAMPLE_DATA` for out-of-bounds parameters.
* `401 Unauthorized` with `ERR_BAD_CREDENTIALS` for invalid login attempts.
---
---

## 3. Database Part

### 1. What main data objects exist?
The schema uses six core tables: `users`, `water_booths`, `quality_samples`, `distribution_schedules`, `incident_reports`, and `system_audit_logs`.

### 2. What fields belong to each data object?
* `water_booths`: `id`, `name`, `latitude`, `longitude`, `status`, `created_at`.
* `quality_samples`: `id`, `booth_id`, `ph_level`, `contamination_level`, `wqi_score`, `safety_classification`, `test_date`, `inspector_id`.
* `incident_reports`: `id`, `reporter_name`, `booth_id`, `description`, `status`, `reported_at`.

### 3. What type is each field?
Identifiers use `UUID`; maps use `NUMERIC(10,7)`; metrics use `NUMERIC(5,2)`; descriptions use `TEXT`; statuses use `ENUM` strings; dates use `TIMESTAMP WITH TIME ZONE`.

### 4. What is the unique key for each data object?
Every primary table leverages an independent `UUIDv4` primary key value. Fields like `users(email)` enforce absolute uniqueness flags at the database constraint level.

### 5. How are data objects connected?
Relationships use explicit Foreign Keys: `quality_samples.booth_id` maps to `water_booths.id`, and `incident_reports.booth_id` references `water_booths.id` (One-to-Many).

### 6. What rules must the data follow?
Database-level constraints guarantee structural integrity: check rules ensure `ph_level` falls within logical boundaries, and `wqi_score` cannot be negative.

### 7. What indexes are needed for fast search?
B-Tree indexes are created on frequently searched fields: `idx_samples_booth_date` for testing history and spatial index definitions for proximity map lookups.

### 8. What data history or audit is needed?
The `system_audit_logs` table operates as an append-only ledger, capturing raw JSON data differentials, active user session tags, and explicit event timestamps.

### 9. How will database changes be made and tracked?
The database schema is managed as code using a version-controlled Flyway migration engine with incremental, version-controlled SQL scripts saved on GitVerse.

### 10. How often is backup done?
The database backup strategy uses two concurrent schedules: automated full database dumps executed nightly at 03:00 MSK alongside continuous WAL replication tracking.

### 11. How is data restored after a failure?
Recovery scripts provision a clean, containerized database target, restore the latest uncorrupted nightly snapshot, and replay the sequential WAL stream.

### 12. What personal data is stored, and how is it protected?
The system limits stored personal data to administrative credentials, names, and contact emails, protected using AES-256 transparent encryption at rest.

### 13. Who can read, add, change, or delete data?
Only the Spring Boot service container can establish active connections to the database port, and it uses a service account barred from executing destructive drop queries.

### 14. How large will the data become over time?
Given 500 distribution nodes throughout Rostov-on-Don producing bi-weekly metrics, data storage calculations estimate an expansion rate of 1.2 Gigabytes per year.

### 15. What test data is needed?
The codebase includes SQL data seed sets to validate local staging builds: mock profiles representing city inspectors and 100 realistic water booth coordinate sets.

---

## 4. Mobile App Part

### 1. Which mobile platforms are needed?
The mobile project delivery strategy compiles application packages to natively support both the iOS (15.0+) and Android (API 26+) operating ecosystems.

### 2. Which devices and screen sizes must be supported?
The visual layouts are explicitly verified against diverse viewport form factors, ranging from compact smartphones up to expansive high-definition tablet resolutions.

### 3. What screens are needed?
The mobile application bundles a focused subset of core views: Interactive Map Screen, Quick-Check Quality Details view, Incident Report Submission form, and Settings.

### 4. How does the user move between screens?
Navigation uses a clean bottom navigation bar for public views. Accessing the inspector workbench triggers a slide-over page transition into an inputs layout.

### 5. What works without internet?
When connectivity is severed, users can still browse the last successfully synchronized water map, review cached safety data, and draft new incident tickets.

### 6. How is data synced when internet returns?
Upon detecting an active internet connection, a background synchronization service serializes the offline actions database queue and flushes changes upstream.

### 7. What notifications are sent?
The app uses native Firebase Cloud Messaging (FCM) to deliver targeted push notifications: immediate safety warnings when a nearby booth is flagged as Unsafe.

### 8. What device permissions are needed?
The application requests specific runtime permissions: Precise Location Access (`ACCESS_FINE_LOCATION`), Camera Access for attachments, and Push Notifications.

### 9. What device features are used?
The application directly calls native hardware subsystems: secure biometric hardware wrappers for user verification and hardware GPS modules for proximity lookups.

### 10. How do login and user verification work?
Public users browse without authentication. Municipal inspectors use an administrative login form that securely stores the returned JWT token within the device keystore.

### 11. What data is stored on the phone?
The app keeps local storage lightweight by using a compact embedded database containing cached booth coordinates, recent regional WQI calculations, and offline logs.

### 12. How are speed, battery use, and mobile data use kept good?
The mobile app uses aggressive HTTP caching for static assets, compresses all image attachments on the client side, and batches analytical telemetry reporting.

### 13. How are app updates released?
Minor updates and text fixes are pushed instantly using Over-The-Air (OTA) frameworks. Major changes are distributed as formal application binary store packages.

### 14. What store rules must be followed?
The release pipeline strictly complies with Apple and Google Developer Policies, including explicit user data privacy declarations and transparent opt-out terms.

### 15. How will the mobile app be tested?
The mobile validation cycle runs automatically across multiple test setups: component unit testing via Jest and end-to-end mobile user journey checking via Maestro.

---

## 5. General Project Questions

### 1. What problem does the project solve?
The project addresses critical public health risks and transparency gaps highlighted by Rospotrebnadzor by providing real-time visibility into water safety in Rostov-on-Don.

### 2. What is inside and outside the project scope?
* Inside Scope: Schema design, Spring Boot REST API code, RBAC configuration, and WQI logic.
* Outside Scope: Physical sampling hardware development and billing modules.

### 3. Who are the users?
The system serves two main user bases within the Rostov Oblast ecosystem: local citizens looking for metrics, and municipal authorities managing water distribution assets.

### 4. What user roles exist?
The software architecture enforces two separate role configurations across its endpoint paths: `ROLE_CITIZEN` and `ROLE_ADMINISTRATOR`.

### 5. What are the main user stories or use cases?
* As a Citizen, I want to view the real-time safety classification of my local water booth.
* As an Inspector, I want to submit recent laboratory parameters to update WQI scores.
* As an Administrator, I want to log a booth outage schedule to warn citizens.

### 6. What must the system do?
The software must process concurrent API calls smoothly, enforce access rules strictly, deliver push alerts within 5 seconds, and keep data streams unified.

### 7. What quality rules must the system meet?
The architecture must maintain a 99.9% operational availability metric, encrypt all transmissions over TLS 1.3, and complete responses under 200ms.

### 8. What limits exist?
Raw inputs are validated against strict logical boundaries (pH 0–14), image attachments are capped at 10MB, and public API endpoints rate limit to 60 hits/minute.

### 9. What assumptions are made?
The specification assumes that municipal laboratories maintain internet access, that users use modern browsers, and that server environments support Docker.

### 10. What risks exist?
Project execution risks include potential data degradation when syncing large offline queues and higher database lock contention near reporting deadlines.

### 11. What is the project timeline and what are the milestones?
The development cycle spans a 16-week academic roadmap: Weeks 1–4 database optimization, Weeks 5–9 Spring Boot setup, and Weeks 14–16 verification via Postman.

### 12. Who is on the team, and what are their duties?
As this is an individual graduation project, it is executed as a solo development effort. The student handles all architecture, coding, database, and QA tasks.

### 13. What rules define acceptance of the project?
The project achieves formal validation when all automated test pipelines pass successfully and a live demonstration proves calculated WQI updates function.

### 14. What documents must be created?
The final project package includes this Technical Specification, an architectural component map, an OpenAPI schema document, and the final graduation thesis text.

### 15. How will GitVerse at gitverse.ru be used for repository, branches, commits, issues, and reviews?
The complete project infrastructure runs on GitVerse using a strict GitFlow policy: active features are built in isolated `feature/*` branches tracking out of `develop`.

---

---

## 6. Deployment Questions

### 1. Which environments are needed, such as development, test, and production?
The system architecture defines three isolated runtime environments: `Development` (local container sandbox for engineering), `Staging` (test environment tracking the main branch used to run end-to-end API suites), and `Production` (stable deployment handling real public water data).

### 2. Where will the application run: own server, cloud, or university server?
The production deployment runs directly onto virtualized private container infrastructures provided by the Southern Federal University laboratory data center in Rostov-on-Don.

### 3. What hardware, operating system, network ports, and firewall rules are needed?
The hardware utilizes managed Ubuntu 22.04 LTS environments allocated with 2 vCPU cores, 4 Gigabytes of RAM, and 30 Gigabytes of persistent SSD storage. Inbound network firewalls block all open communication paths except Port 443 (HTTPS access) and Port 22 (SSH lines restricted to university network configurations).

### 4. How is the frontend built and served?
The web static layers are compiled using a multi-stage Docker build container. The optimized HTML5, JavaScript, and asset artifacts are then copied into a minimal, hardened Nginx alpine image configured to serve files efficiently and handle client-side routing fallbacks.

### 5. How is the backend built and started?
The Spring Boot java application uses Maven to build a production-ready JAR executable. This package runs within an official Eclipse Temurin OpenJDK Docker base container, using active configuration parameters passed through environment variables.

### 6. How is the database installed and connected?
The database layer establishes connectivity with an independent database instance hosted via a Docker container. Network policies prevent any outside connection to its port, while the Spring Boot runtime authenticates using tightly scoped service credentials.

### 7. How are database changes applied?
Database structural updates are tracked as version-controlled migration scripts. When the container boots up, the Flyway migration tool scans the target cluster schema, determines missing update logs, and runs pending changes seamlessly before allowing incoming connection queries.

### 8. How are settings and secrets stored?
Environment configurations are decoupled completely from the compiled code. General parameter properties reside in external environmental profiles, while production database credentials, API access keys, and JWT cryptography vectors are dynamically loaded using system secrets handlers.

### 9. What web server, domain name, and SSL certificate are used?
The environment handles external user paths through a hardened Nginx reverse proxy instance routing traffic to a dedicated university subdomain (e.g., `water.cs.sfedu.ru`), secured using Let's Encrypt SSL certificates which are renewed automatically by a local background cron job.

### 10. How is automatic build and release done?
The repository leverages GitVerse CI pipelines. Every push to the main branch triggers automated actions: the pipeline builds the Spring Boot artifacts, runs unit testing validation layers, constructs fresh production Docker images, and deploys them to the staging environment without service downtime.

### 11. How are monitoring and health checks done?
The Spring Boot backend includes the Spring Boot Actuator module. This infrastructure exposes a specialized path (`/actuator/health`) that actively tracks microservice operational viability, database connectivity states, and node resource metrics.

### 12. Where are logs collected, and how long are they kept?
Container application streams are aggregated using system logging daemons. Files are rolled daily, written to isolated network storage volumes, and maintained for a strict 30-day window to provide reliable security audits.

### 13. How are backups and recovery done?
The data infrastructure utilizes automated cron scripts that generate snapshot dumps during low-usage windows (02:00 UTC). These archives are compressed, encrypted with an AES-256 key, and safely uploaded to a distinct server machine to ensure complete recovery points during major system failures.

### 14. How is rollback to a previous version done?
If a broken release passes staging checks, administrators can trigger immediate version rollbacks through the GitVerse image registry by re-pointing active container deployment tags to the previous stable release version.

### 15. How are scaling, access control, and cost managed?
The lightweight nature of the Spring Boot container allows for simple horizontal replication behind the Nginx load balancer if traffic peaks. Running all instances on internal university hardware keeps external cloud platform infrastructure costs at zero.

