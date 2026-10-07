# ResortsLite — Java 11 Demo Application

A compact Spring Boot 2.7.x resort booking application, migrated from Java 8 to Java 11.
It still carries the **intentional legacy patterns** across the Cloud Compatibility,
Software Portability, Code Sustainability and Security Health domains (see the
Violation Traceability Matrix below) — only the JDK version and the two unused,
CVE-flagged dependencies (Log4j / commons-collections) were addressed as part of
this migration. All other seeded violations are untouched and out of scope.

**Purpose:** Hands-on Concierto Modernize demo — scan, assess, and transform.

---

## Tech Stack

| Item | Version |
|---|---|
| Java | 11 |
| Spring Boot | 2.7.18 |
| Spring MVC | 5.3.x |
| Build | Maven |
| Database | H2 in-memory |

---

## Violation Traceability Matrix

| Rule ID | Domain | Severity | File | Line(s) | Description |
|---|---|---|---|---|---|
| cr-java-0065 | Cloud Compatibility | Mandatory | BookingController.java | 33, 34, 48 | HTTP session state storage — breaks auto-scaling |
| cr-java-0067 | Cloud Compatibility | Potential | BookingController.java | 18 | In-memory cache without TTL — instance-local |
| cr-java-0088 | Cloud Compatibility | Mandatory | BookingController.java | 60 | Plain HTTP URL for internal service call |
| cr-java-0088 | Cloud Compatibility | Mandatory | ReportService.java | 59 | Plain HTTP URL for report download |
| cr-java-0021 | Cloud Compatibility | Mandatory | BookingService.java | 20, 25 | Hardcoded DB hostname + payment API endpoint |
| cr-java-0021 | Cloud Compatibility | Mandatory | application.properties | 12–17 | Hardcoded internal service endpoints |
| czr-java-001 | Software Portability | Mandatory | BookingController.java | 71 | Hardcoded absolute file path in controller |
| czr-java-001 | Software Portability | Mandatory | ReportService.java | 17, 20 | Hardcoded Linux + Windows absolute paths |
| czr-port-001 | Software Portability | High | ReportService.java | 24 | Fixed server port — blocks ECS/EKS dynamic binding |
| sql-inject-001 | Security Health | Critical | BookingService.java | 36–38 | SQL injection via string concatenation (INSERT) |
| sql-inject-001 | Security Health | Critical | BookingService.java | 53 | SQL injection via string concatenation (SELECT) |
| sec-cred-001 | Security Health | Critical | BookingService.java | 21, 22 | Hardcoded database credentials in source code |
| sec-weak-hash-001 | Security Health | High | BookingService.java | 43, 91, 92 | MD5 used for confirmation code hashing |
| CVE-2021-44228 | Security Health | Critical | pom.xml | ~~35~~ | **Remediated in Java 11 migration** — unused Log4j 2.14.1 dependency removed |
| CVE-2015-6420 | Security Health | High | pom.xml | ~~41~~ | **Remediated in Java 11 migration** — unused commons-collections 3.2.1 dependency removed |
| dup-logic-001 | Code Sustainability | Medium | BookingService.java | 71–72 | Duplicated room type validation |
| complexity-001 | Code Sustainability | High | BookingService.java | 65–82 | Cyclomatic complexity > 9 in calculateRoomPrice |
| doc-missing-001 | Code Sustainability | Medium | ReportService.java | 55, 63 | Missing JavaDoc on public methods |

---

## Expected COMPASS Scores (Pre-Transformation)

| Domain | Expected Score | Primary Driver |
|---|---|---|
| Cloud Compatibility | ~55 / 100 | 6 cloud blockers (session, config, HTTP) |
| Software Portability | ~70 / 100 | Hardcoded paths + fixed port |
| Code Sustainability | ~65 / 100 | High complexity + duplication + missing docs |
| Security Health | ~45 / 100 | 2 critical CVEs + SQL injection + hardcoded creds |

---

## How to Run

```bash
mvn spring-boot:run
```

App starts on http://localhost:8080

**H2 Console:** http://localhost:8080/h2-console

**Sample Endpoints:**
```
POST /api/bookings/create?guestName=John&roomType=SUITE&checkIn=2024-06-01&checkOut=2024-06-05
GET  /api/bookings/status/{bookingId}
GET  /api/bookings/availability?roomType=DELUXE
GET  /api/bookings/report/download?month=june
```

---

## Line Count Summary

| File | Lines |
|---|---|
| pom.xml | 54 |
| ResortsLiteApplication.java | 11 |
| BookingController.java | 82 |
| BookingService.java | 100 |
| ReportService.java | 69 |
| application.properties | 18 |
| **Total** | **334** |

*Java source lines only: 262*
