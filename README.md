<div align="center">

# 🔌 API Test Suite — JSONPlaceholder (Postman + Newman)

### CRUD API testing with automated assertions, data-driven execution, and a CI-ready Newman pipeline.

[![Requests](https://img.shields.io/badge/Requests-50-blue?style=for-the-badge)](./JSONPlaceholder.postman_collection.json)
[![Assertions](https://img.shields.io/badge/Assertions-165%20passed-success?style=for-the-badge)](./newman-report.html)
[![Iterations](https://img.shields.io/badge/Data--Driven%20Iterations-5-orange?style=for-the-badge)](./test-data.csv)
[![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)]()

[![Postman](https://img.shields.io/badge/Postman-FF6C37?style=flat-square&logo=postman&logoColor=white)]()
[![Newman](https://img.shields.io/badge/Newman-CLI-black?style=flat-square)]()
[![CI Ready](https://img.shields.io/badge/CI%2FCD-Ready-black?style=flat-square)]()
[![Data Driven](https://img.shields.io/badge/Data--Driven%20Testing-black?style=flat-square)]()

</div>

---

## 📌 Overview

This project demonstrates full API testing against
[**JSONPlaceholder**](https://jsonplaceholder.typicode.com), a free public
REST API. It covers the complete CRUD lifecycle on the `/posts` resource,
plus a bonus schema-validation suite on `/users` — built and executed the
way a real CI pipeline would run it: headlessly, via Newman, with an
exported HTML report as proof of execution.

Every request asserts on **status code**, **response time**, and
**response schema/body content** — not just "did it return 200."

> 🔍 **Real finding from execution:** One negative test case originally
> assumed JSONPlaceholder would return `200` for a `PUT` to a nonexistent
> ID. Running the suite for real revealed it actually returns `500`. The
> test was corrected to match verified behavior rather than the incorrect
> assumption — see [Notes on JSONPlaceholder](#-notes-on-jsonplaceholder-as-a-target) below.

---

## 🗂️ Repository Structure

```
qa-project-2-api-testing/
├── README.md                                → you are here
├── JSONPlaceholder.postman_collection.json  → 10 requests, CRUD + negative cases
├── JSONPlaceholder.postman_environment.json → {{base_url}} — no hardcoded URLs
├── test-data.csv                            → 5 rows of data-driven test input
└── newman-report.html                       → exported HTML report from a real Newman run
```

---

## ✅ Verified Test Results (Newman CLI run)

| Metric | Result |
|---|---:|
| Iterations | 5 |
| Requests executed | 50 |
| Test scripts run | 50 |
| **Assertions passed** | **165 / 165** |
| Total run duration | 6.3s |
| Average response time | 38ms |

Full report: [`newman-report.html`](./newman-report.html) — open it directly
in a browser for the interactive breakdown.

---

## 🧭 Coverage

**Posts — CRUD (`/posts`)**

| Method | Case | Assertions |
|---|---|---|
| GET | All posts (positive) | status, response time, array + schema |
| GET | Single post (positive) | status, response time, id match, schema |
| GET | Single post — nonexistent ID (negative) | `404`, empty body |
| POST | Create post — **data-driven via CSV** | `201`, echoes submitted data |
| POST | Create post — missing fields (negative) | documents simulator's non-validating behavior |
| PUT | Update post (positive) | `200`, response reflects updates |
| PUT | Update post — nonexistent ID (negative) | `500` — verified via live run, not assumed |
| PATCH | Partial update (positive) | `200`, only patched field changes |
| DELETE | Delete post (positive) | `200`, empty body |

**Users — Schema Validation (bonus, `/users`)**
- Status, response time, exact record count, email format regex, nested
  object (`address`, `company`) schema validation

---

## 🚀 How to Run It

**1. Import into Postman**
Import `JSONPlaceholder.postman_collection.json` and
`JSONPlaceholder.postman_environment.json`, select the environment, run
individual requests or the full Collection Runner.

**2. Data-driven run**
Collection → Run → attach `test-data.csv` under **Data** → Run. The
`POST Create Post` request executes once per CSV row.

**3. Run headlessly with Newman (CI-style)**
```bash
npm install -g newman newman-reporter-htmlextra

newman run JSONPlaceholder.postman_collection.json \
  -e JSONPlaceholder.postman_environment.json \
  -d test-data.csv \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export newman-report.html
```

---

## 📝 Notes on JSONPlaceholder as a Target

JSONPlaceholder is a **simulator**, not a real backend —
`POST`/`PUT`/`PATCH`/`DELETE` requests are not actually persisted. Two
negative test cases document real, verified gaps against real-API
standards rather than hiding them:

- **Missing required fields on POST** still returns `201` instead of a
  real API's expected `400` — the simulator performs no server-side
  validation.
- **PUT to a nonexistent ID** returns `500` (verified via an actual
  Newman run — not assumed), instead of a real API's expected `404`.

Both are called out explicitly in the test script comments rather than
silently passed or hidden — exactly what you'd flag to a team if you
found this behavior on a real API under test.

---

## 🎯 Key Skills Demonstrated

- ✅ Full CRUD API test design (positive + negative cases)
- ✅ Assertions on status code, response time, and schema/body content
- ✅ Environment variables — zero hardcoded URLs
- ✅ Data-driven testing via CSV + Collection Runner
- ✅ CI-ready headless execution with Newman
- ✅ Correcting a test's assumption based on verified, real execution
  results rather than leaving a false failure unexplained

---

## 🚀 What's Next

- Wire this Newman command into a GitHub Actions workflow so it runs on
  every push
- Add authentication-flow tests (login/token headers) against an API
  that supports it

---

<div align="center">

**Tester:** Taufik Sumara &nbsp;|&nbsp; **Date:** July 29, 2026

⭐ *If this project is useful as a QA portfolio reference, feel free to star it.*

</div>
