4.1 Key Components



●	Client layer: Flutter apps for iOS, Android and Web, plus a data feed from wearables/sensors (HealthKit, Google Fit, Bluetooth Low Energy devices).

●	API Gateway: single entry point (NGINX) that handles routing, rate limiting and TLS termination before requests reach any backend service.

●	Auth Service: handles login, token issuance (JWT) and MFA via Auth0, sitting alongside the gateway so every downstream service can validate tokens consistently.

●	Core API Service (NestJS): owns workouts, nutrition tracking and social features; the main business-logic service.

●	AI Microservice (FastAPI): generates personalised workout and nutrition plans using ML models, kept separate so it can scale and deploy independently of the core API.

●	Real-time Service: WebSocket-based service for live workout tracking and social feed updates.

●	Cache layer (Redis): stores sessions and frequently accessed data to keep response times low.

●	Databases: PostgreSQL for structured/relational data, MongoDB for activity logs and AI feature data, S3-compatible object storage for media (progress photos, workout videos) and data exports.

●	External integrations: nutrition database APIs, push notification services (FCM/APNs), a payment gateway for subscriptions, and health data sync (HealthKit/Google Fit).

●	Monitoring \& logging: Prometheus, Grafana and Sentry for observability and error tracking across all services.



4.2 Data Flow Examples

Personalised workout plan

Client → API Gateway → Auth Service (validate token) → Core API Service → AI Microservice (requests a plan based on stored user history from MongoDB/PostgreSQL) → AI Microservice returns the plan → Core API Service stores/caches it → response sent back to the client, with Redis caching the plan for quick re-access.

Social sharing

Client posts an update → API Gateway → Core API Service writes the post to PostgreSQL/MongoDB and pushes it to the Real-time Service → Real-time Service broadcasts the update over WebSocket to followers currently connected → offline followers receive a push notification via FCM/APNs on their next app open.

Nutrition tracking

Client logs a meal → API Gateway → Core API Service calls the external Nutrition DB API to resolve nutritional values → result stored in MongoDB (flexible schema for varying food data) → aggregated daily totals cached in Redis for fast dashboard rendering.

4.3 Security, Scalability and Integration Considerations



●	Security: all traffic runs over TLS; JWTs are short-lived with refresh tokens; sensitive health data in PostgreSQL/MongoDB is encrypted at rest; Auth0's HIPAA BAA and GDPR tooling cover compliance requirements for health-adjacent data; the API Gateway enforces rate limiting to reduce abuse.

●	Scalability: the Core API, AI Microservice and Real-time Service are deployed as independent containers so each can be scaled horizontally on its own (the AI service, for example, can scale separately during peak plan-generation times); Redis caching and read replicas on PostgreSQL reduce database load.

●	Integration: the API Gateway gives one consistent entry point for all client platforms; external integrations (nutrition API, payments, push notifications) are isolated behind the Core API so a change in a third-party provider doesn't ripple through the whole system.



4.4 Architecture Decision Record (ADR)



**ADR Title**

Adopt a Flutter + NestJS + FastAPI microservice architecture for FitFlow



**Status**

Accepted



**Context**

Needs a consistent iOS/Android/Web experience

Requires AI-driven personalisation

Needs real-time features

Must be maintainable by a mid-sized team

Must comply with health-data regulations (HIPAA/GDPR)



**Decision**

Client: Flutter for a single cross-platform client

Backend: Split into NestJS core API + separate FastAPI AI microservice

Database: PostgreSQL for relational data, MongoDB for flexible activity/AI data

Auth: Auth0 for authentication

Infra: API Gateway in front of all services



**Alternatives Considered**

Stack B: React Native + FastAPI + Firebase Auth

Stack C: Fully native (Swift/Kotlin) + Go + Cognito



Both scored lower on the weighted decision matrix in Activity 3 — mainly due to reusability and development-speed trade-offs



**Consequences**

**Positive:**



Single client codebase reduces maintenance cost



Separating the AI service lets it scale and deploy independently



Auth0 simplifies compliance



**Negative:**



Team needs to manage two backend languages (TypeScript and Python)



Flutter developers may need extra ramp-up time for native platform channels (deep HealthKit/Google Fit integration)



