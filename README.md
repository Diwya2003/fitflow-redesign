\# FitFlow Redesign



A cross-platform fitness application redesign for IT3060 – Human Computer Interaction (Semester 2, 2026).



\## Tech Stack



| Layer | Technology | Reason |

|---|---|---|

| Frontend | React Native + TypeScript + Expo | 50–70% code reuse, mature HealthKit/Google Fit libraries |

| Backend | Python / FastAPI | Best AI/ML integration, async support |

| Database | PostgreSQL | ACID compliance, ideal for structured health data |

| Cache | Redis | Low-latency session \& hot-data caching |

| Auth | Firebase Auth | Fast setup, MFA, social login |

| AI Service | Python microservice | Personalized workout \& nutrition recommendations |



\## Documentation



\- \[Tech Comparison](docs/comparison.md)

\- \[Decision Matrix](docs/matrix.md)

\- \[Architecture Diagram](docs/architecture.png)

\- \[Architecture Decision Record](docs/adr.md)



\## Compliance Notes



\- \*\*GDPR:\*\* Explicit user consent required for processing health data (Article 9 – special category data).

\- \*\*HIPAA:\*\* Not applicable if only consumer wellness data is processed. A BAA is required if integrated with healthcare providers.



\## Repository Structure



