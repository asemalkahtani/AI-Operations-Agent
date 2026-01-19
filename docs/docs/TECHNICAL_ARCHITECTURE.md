# Thabot Alkahtani - Technical Architecture

**Document ID:** AJRC-TECH-2026-001  
**Date:** January 18, 2026  
**Owner:** Al Jazeerah River Company  
**Prepared by:** Dr. Asem Alkahtani

---

## 1. System Architecture Overview

Thabot operates on a **Three-Layer Architecture Design**:

```
┌─────────────────────────────────────────────────────────┐
│         LAYER 1: FRONTEND - "The Shield"               │
│  (Public-facing, Customer Interactions, Filtered Data)  │
├─────────────────────────────────────────────────────────┤
│  Technologies:                                          │
│  • Vertex AI Agent Builder                             │
│  • Dialogflow CX                                        │
│  • Web/Mobile Interface                                │
│  • Rate limiting & DDoS protection                      │
└─────────────────────────────────────────────────────────┘
                        ↓ (Secured API)
┌─────────────────────────────────────────────────────────┐
│      LAYER 2: BACKEND - "The Engine"                   │
│  (Business Logic, AI Processing, Role-Based Access)     │
├─────────────────────────────────────────────────────────┤
│  Technologies:                                          │
│  • Google Cloud Run (Microservices)                    │
│  • Gemini 1.5 Pro/Flash AI Models                      │
│  • Odoo ERP API Integration                            │
│  • Business Logic Processors                           │
│  • Anomaly Detection Engine                            │
│  • Predictive Analytics                                │
└─────────────────────────────────────────────────────────┘
                        ↓ (Encrypted Connection)
┌─────────────────────────────────────────────────────────┐
│      LAYER 3: DATABASE - "The Vault"                   │
│  (Secure Data Storage, Model Training, Audit Logs)      │
├─────────────────────────────────────────────────────────┤
│  Technologies:                                          │
│  • Google Cloud SQL (PostgreSQL 15+)                   │
│  • Google BigQuery (Analytics & ML Training)            │
│  • Cloud Storage (Files & Attachments)                 │
│  • Encryption at-rest (CMEK) & in-transit             │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Cloud Infrastructure (Google Cloud Platform)

### 2.1 Regional Deployment

| Component | Technology | Region | Specification |
|-----------|-----------|--------|-----------------|
| **Compute** | Compute Engine | me-central2 (Dammam, KSA) | C4-Standard-8 (8 vCPU, 30GB RAM) |
| **Database** | Cloud SQL | me-central2 (Dammam, KSA) | PostgreSQL 15+ HA with sync replication |
| **Analytics** | BigQuery | me-central2 (Dammam, KSA) | Multi-region dataset |
| **Storage** | Cloud Storage | me-central2 (Dammam, KSA) | Standard class for files/attachments |
| **Security** | Cloud Armor | Global | DDoS and SQL injection protection |
| **Networking** | VPC | me-central2 (Dammam, KSA) | Private subnets with firewall rules |

**Data Sovereignty Guarantee:** All data remains within Saudi Arabia (me-central2 region). No cross-border data transfer.

### 2.2 Network Architecture

```
┌────────────────────────────────────────────┐
│     External Users / Customers              │
│  (Internet, Mobile Apps, Web Browsers)      │
└────────────────┬─────────────────────────────┘
                 │
                 ↓ (HTTPS/TLS 1.3)
        ┌─────────────────────┐
        │  Cloud Load         │
        │  Balancer           │
        │  + Cloud Armor      │
        │  (DDoS Protection)  │
        └────────┬────────────┘
                 │
        ┌────────▼──────────────────────────┐
        │   VPC - me-central2 Region        │
        │                                   │
        │  ┌──────────────────────────────┐ │
        │  │  Public Subnet               │ │
        │  │  (Frontend Tier)             │ │
        │  │  • Vertex AI Agent Builder   │ │
        │  │  • Cloud Run (Frontend)      │ │
        │  │  • Dialogflow CX             │ │
        │  └────────┬─────────────────────┘ │
        │           │                       │
        │  ┌────────▼─────────────────────┐ │
        │  │  Private Subnet              │ │
        │  │  (Application Tier)          │ │
        │  │  • Cloud Run (Backend)       │ │
        │  │  • API Services              │ │
        │  │  • Processing Engine         │ │
        │  └────────┬─────────────────────┘ │
        │           │                       │
        │  ┌────────▼─────────────────────┐ │
        │  │  Database Tier               │ │
        │  │  • Cloud SQL (PostgreSQL)    │ │
        │  │  • BigQuery (Analytics)      │ │
        │  │  • Cloud Storage             │ │
        │  └──────────────────────────────┘ │
        │                                   │
        └───────────────────────────────────┘
```

---

## 3. Layer 1: Frontend - "The Shield"

### 3.1 Purpose

- **Public-facing interface** for customer interactions
- - **Safe filtering** of sensitive requests
  - - **Intelligent routing** of complex queries
    - - **Rate limiting** to prevent abuse
      - - **Multi-language support** (Arabic Saudi dialect + English)
       
        - ### 3.2 Technologies
       
        - | Component | Technology | Purpose |
        - |-----------|-----------|---------|
        - | **Conversational AI** | Dialogflow CX | Natural language understanding |
        - | **Agent Framework** | Vertex AI Agent Builder | Orchestration & routing |
        - | **Web Interface** | React + TailwindCSS | User-friendly UI |
        - | **Mobile Interface** | Flutter/React Native | iOS & Android apps |
        - | **API Gateway** | Cloud Endpoints | Request validation & routing |
       
        - ### 3.3 Core Functions
       
        - ```
          Customer Query
              ↓
          ┌─────────────────────────────┐
          │ Language Processing         │
          │ (Arabic/English detection)  │
          └────────┬────────────────────┘
                   ↓
          ┌─────────────────────────────┐
          │ Intent Classification       │
          │ (FAQ, Report, Escalation)   │
          └────────┬────────────────────┘
                   ↓
          ┌─────────────────────────────┐
          │ Sensitivity Detection       │
          │ (Filter sensitive queries)  │
          └────────┬────────────────────┘
                   ↓
              [DECISION]
              /        \
             /          \
          Safe Query   Sensitive Query
            ↓            ↓
          Backend API  Escalate to Human
          ```

          ### 3.4 Access Control

          | User Type | Access Level | Functions |
          |-----------|-------------|-----------|
          | **Customer** | Level 4 (Limited) | FAQ, General inquiries |
          | **Support Staff** | Level 3 (Normal) | Customer queries, Basic reports |
          | **Management** | Level 2 (Operational) | Detailed reports, Analytics |
          | **IT Director** | Level 1 (Full Admin) | All functions + System control |

          ---

          ## 4. Layer 2: Backend - "The Engine"

          ### 4.1 Purpose

          - **Business logic processing** for complex operations
          - - **AI decision-making** using Gemini models
            - - **Data integration** with Odoo ERP
              - - **Real-time monitoring** and anomaly detection
                - - **Report generation** and insights
                 
                  - ### 4.2 Technologies
                 
                  - | Component | Technology | Purpose |
                  - |-----------|-----------|---------|
                  - | **Serverless Compute** | Google Cloud Run | Microservices execution |
                  - | **AI Models** | Gemini 1.5 Pro/Flash | Deep analysis & reasoning |
                  - | **Integration Middleware** | Cloud Tasks | Async job processing |
                  - | **Message Queue** | Pub/Sub | Event-driven architecture |
                  - | **Caching Layer** | Memorystore (Redis) | Performance optimization |
                 
                  - ### 4.3 Microservices Architecture
                 
                  - ```
                    ┌──────────────────────────────────────────────────────┐
                    │              Cloud Run (Microservices)               │
                    ├──────────────────────────────────────────────────────┤
                    │                                                      │
                    │  ┌─────────────────┐      ┌──────────────────┐     │
                    │  │ API Service     │      │ Auth Service     │     │
                    │  │ (REST/GraphQL)  │      │ (JWT/OAuth2)     │     │
                    │  └────────┬────────┘      └────────┬─────────┘     │
                    │           │                        │               │
                    │  ┌────────▼──────────────────┐   ┌▼──────────────┐ │
                    │  │ Business Logic Service    │   │ User Service  │ │
                    │  │ (Core Processing)         │   │ (Management)  │ │
                    │  └────────┬──────────────────┘   └───────────────┘ │
                    │           │                                        │
                    │  ┌────────▼──────────────────────────────────────┐ │
                    │  │ AI Engine (Gemini Integration)               │ │
                    │  │ • Deep Analysis (1.5 Pro)                    │ │
                    │  │ • Quick Responses (Flash)                    │ │
                    │  │ • Model selection based on query complexity  │ │
                    │  └────────┬──────────────────────────────────────┘ │
                    │           │                                        │
                    │  ┌────────▼──────────────────────────────────────┐ │
                    │  │ Integration Service                          │ │
                    │  │ • Odoo ERP API                               │ │
                    │  │ • ZATCA E-invoicing                          │ │
                    │  │ • Email Systems                              │ │
                    │  │ • IoT Devices                                │ │
                    │  └────────┬──────────────────────────────────────┘ │
                    │           │                                        │
                    │  ┌────────▼──────────────────────────────────────┐ │
                    │  │ Analytics Service                            │ │
                    │  │ • Anomaly Detection                          │ │
                    │  │ • Predictive Analytics                       │ │
                    │  │ • Performance Metrics                        │ │
                    │  └────────┬──────────────────────────────────────┘ │
                    │           │                                        │
                    │  ┌────────▼──────────────────────────────────────┐ │
                    │  │ Logging & Audit Service                      │ │
                    │  │ • All interactions logged                    │ │
                    │  │ • Compliance tracking                        │ │
                    │  │ • Security event monitoring                  │ │
                    │  └────────────────────────────────────────────────┘ │
                    │                                                      │
                    └──────────────────────────────────────────────────────┘
                    ```

                    ### 4.4 AI Model Strategy

                    #### Gemini 1.5 Pro
                    - **Use Case**: Deep analysis, complex reports, strategic decisions
                    - - **Capabilities**: Extended context (up to 1M tokens), multimodal analysis
                      - - **Response Time**: 5-10 seconds (acceptable for detailed reports)
                        - - **Cost**: Higher (used for complex queries only)
                         
                          - #### Gemini 1.5 Flash
                          - - **Use Case**: Quick responses, FAQ, real-time chat
                            - - **Capabilities**: Fast processing, sufficient context
                              - - **Response Time**: < 2 seconds (acceptable for customer chat)
                                - - **Cost**: Lower (used for general queries)
                                 
                                  - **Model Selection Logic:**
                                  - ```
                                    Query Classification
                                        ↓
                                    [Complexity Check]
                                        ├─ Simple FAQ? → Use Flash (fast + cheap)
                                        ├─ Requires context? → Use Flash
                                        └─ Complex analysis needed? → Use Pro (accurate + comprehensive)
                                    ```

                                    ### 4.5 Integration Points

                                    #### Odoo ERP Integration
                                    ```
                                    Thabot Backend
                                        ↓
                                    [REST API Layer]
                                        ↓
                                    Odoo ERP
                                    ├─ Sales Module
                                    ├─ Inventory Management
                                    ├─ Accounting
                                    ├─ Purchase Orders
                                    └─ Reports
                                    ```

                                    #### ZATCA E-Invoicing
                                    - Real-time compliance checking
                                    - - Invoice validation before submission
                                      - - Automated compliance reporting
                                        - - Government portal integration
                                         
                                          - #### Email Systems
                                          - - Automated notifications
                                            - - Report distribution
                                              - - Alert delivery
                                                - - User communications
                                                 
                                                  - #### IoT Device Integration
                                                  - - Sensor data ingestion
                                                    - - Real-time monitoring
                                                      - - Anomaly alerts
                                                        - - Device health tracking
                                                         
                                                          - ---

                                                          ## 5. Layer 3: Database - "The Vault"

                                                          ### 5.1 Purpose

                                                          - **Transactional data storage** (Cloud SQL)
                                                          - - **Analytics & ML training** (BigQuery)
                                                            - - **File & attachment storage** (Cloud Storage)
                                                              - - **Audit logging** for compliance
                                                                - - **Data security** with encryption
                                                                 
                                                                  - ### 5.2 Database Design
                                                                 
                                                                  - #### Cloud SQL (PostgreSQL 15+)
                                                                 
                                                                  - **High Availability Setup:**
                                                                  - ```
                                                                    Primary Database (me-central2)
                                                                        ↓ (Synchronous Replication)
                                                                    Standby Database (me-central2)
                                                                        ↓ (Asynchronous Backup)
                                                                    Backup in Cold Storage
                                                                    ```

                                                                    **Database Schema:**

                                                                    ```
                                                                    ┌─────────────────────────────┐
                                                                    │ Users Table                 │
                                                                    ├─────────────────────────────┤
                                                                    │ user_id (PK)               │
                                                                    │ username                   │
                                                                    │ email                      │
                                                                    │ password_hash              │
                                                                    │ role                       │
                                                                    │ created_at                 │
                                                                    │ updated_at                 │
                                                                    └─────────────────────────────┘
                                                                             ↓
                                                                    ┌─────────────────────────────┐
                                                                    │ Operations Table            │
                                                                    ├─────────────────────────────┤
                                                                    │ operation_id (PK)          │
                                                                    │ user_id (FK)               │
                                                                    │ type                       │
                                                                    │ status                     │
                                                                    │ data (JSON)                │
                                                                    │ created_at                 │
                                                                    │ updated_at                 │
                                                                    └─────────────────────────────┘
                                                                             ↓
                                                                    ┌─────────────────────────────┐
                                                                    │ Audit Logs Table            │
                                                                    ├─────────────────────────────┤
                                                                    │ log_id (PK)                │
                                                                    │ user_id (FK)               │
                                                                    │ action                     │
                                                                    │ resource                   │
                                                                    │ timestamp                  │
                                                                    │ ip_address                 │
                                                                    │ details (JSON)             │
                                                                    └─────────────────────────────┘
                                                                    ```

                                                                    #### BigQuery (Analytics)

                                                                    ```
                                                                    Raw Data Layer
                                                                        ↓
                                                                    [ETL Pipeline - Cloud Dataflow]
                                                                        ↓
                                                                    Structured Data Warehouse
                                                                        ├─ Operations Analytics
                                                                        ├─ User Behavior Analysis
                                                                        ├─ Performance Metrics
                                                                        ├─ Financial Reports
                                                                        └─ Compliance Dashboards
                                                                        ↓
                                                                    [ML Training Data]
                                                                        ↓
                                                                    Model Improvement
                                                                    ```

                                                                    ### 5.3 Security & Encryption

                                                                    #### At-Rest Encryption
                                                                    - **CMEK** (Customer-Managed Encryption Keys)
                                                                    - - Keys stored in Cloud KMS
                                                                      - - Separate keys for different data classifications
                                                                        - - Automatic key rotation
                                                                         
                                                                          - #### In-Transit Encryption
                                                                          - - **TLS 1.3** for all connections
                                                                            - - Certificate pinning for APIs
                                                                              - - Encrypted VPN for admin access
                                                                                - - mTLS for service-to-service communication
                                                                                 
                                                                                  - #### Data Classification
                                                                                 
                                                                                  - | Classification | Storage | Encryption | Access |
                                                                                  - |---------------|---------|-----------|--------|
                                                                                  - | **Public** | Cloud Storage | Standard | Everyone |
                                                                                  - | **Internal** | Cloud SQL | CMEK | Employees |
                                                                                  - | **Confidential** | Separated table | CMEK | Authorized only |
                                                                                  - | **Restricted** | Separate database | CMEK + Hardware Key | IT Director only |
                                                                                 
                                                                                  - ---

                                                                                  ## 6. Security Architecture

                                                                                  ### 6.1 Defense in Depth

                                                                                  ```
                                                                                  Layer 1: Network Level
                                                                                  ├─ Cloud Armor (DDoS protection)
                                                                                  ├─ Cloud CDN (Caching & security)
                                                                                  ├─ VPC Firewall (Inbound/outbound rules)
                                                                                  └─ VPC Service Controls (API access policies)

                                                                                  Layer 2: Application Level
                                                                                  ├─ OAuth 2.0 / OIDC (Authentication)
                                                                                  ├─ Role-Based Access Control (Authorization)
                                                                                  ├─ Input validation & sanitization
                                                                                  ├─ Rate limiting & throttling
                                                                                  └─ API key management

                                                                                  Layer 3: Data Level
                                                                                  ├─ Encryption at-rest (CMEK)
                                                                                  ├─ Encryption in-transit (TLS 1.3)
                                                                                  ├─ Data masking for sensitive fields
                                                                                  ├─ Audit logging
                                                                                  └─ Access control lists

                                                                                  Layer 4: Compliance Level
                                                                                  ├─ PDPL compliance checking
                                                                                  ├─ NCA security requirements
                                                                                  ├─ ZATCA audit trail
                                                                                  └─ Regular security assessments
                                                                                  ```

                                                                                  ### 6.2 Access Control Matrix

                                                                                  ```
                                                                                  Resource               | Customer | Staff | Manager | IT Dir
                                                                                  ─────────────────────────────────────────────────────────────
                                                                                  Customer FAQ           |   ✓     |   ✓   |    ✓    |   ✓
                                                                                  Operations Reports     |   ✗     |   ✓   |    ✓    |   ✓
                                                                                  Financial Data         |   ✗     |   ✗   |    ✓    |   ✓
                                                                                  System Configuration   |   ✗     |   ✗   |    ✗    |   ✓
                                                                                  User Management        |   ✗     |   ✗   |    ✗    |   ✓
                                                                                  Emergency Shutdown     |   ✗     |   ✗   |    ✗    |   ✓
                                                                                  ```

                                                                                  ---

                                                                                  ## 7. Scalability & Performance

                                                                                  ### 7.1 Horizontal Scaling

                                                                                  ```
                                                                                  Low Load                      High Load
                                                                                  (1-100 RPS)                   (1000+ RPS)

                                                                                  1 Cloud Run Instance  →  Auto-scaling to N Instances
                                                                                  Single Database        →  Read Replicas
                                                                                  No Cache              →  Redis Cache Layer
                                                                                  ```

                                                                                  ### 7.2 Performance Targets

                                                                                  | Metric | Target | Method |
                                                                                  |--------|--------|--------|
                                                                                  | **API Latency (p95)** | < 500ms | Cloud Run + Caching |
                                                                                  | **FAQ Response Time** | < 2 seconds | Flash model + Cache |
                                                                                  | **Report Generation** | < 30 seconds | Pro model async |
                                                                                  | **Data Ingestion** | Real-time | Pub/Sub streaming |
                                                                                  | **Search Time** | < 1 second | BigQuery + Cache |

                                                                                  ### 7.3 Disaster Recovery

                                                                                  ```
                                                                                  RTO: 4 Hours (Recovery Time Objective)
                                                                                  RPO: 1 Hour (Recovery Point Objective)

                                                                                  Backup Strategy:
                                                                                  ├─ Continuous replication to standby
                                                                                  ├─ Hourly snapshots to Cold Storage
                                                                                  ├─ Daily backups to separate region (planned)
                                                                                  ├─ Point-in-time recovery (up to 30 days)
                                                                                  └─ Tested recovery procedures monthly
                                                                                  ```

                                                                                  ---

                                                                                  ## 8. Monitoring & Observability

                                                                                  ### 8.1 Monitoring Stack

                                                                                  ```
                                                                                  Application Metrics
                                                                                      ├─ API response times
                                                                                      ├─ Error rates
                                                                                      ├─ AI model accuracy
                                                                                      └─ User activity logs
                                                                                           ↓
                                                                                  Cloud Monitoring Dashboard
                                                                                      ├─ Real-time alerts
                                                                                      ├─ Performance graphs
                                                                                      ├─ Anomaly detection
                                                                                      └─ Trend analysis
                                                                                           ↓
                                                                                  [Alert Triggers]
                                                                                      ├─ High error rate > 1%
                                                                                      ├─ Response time > 2s
                                                                                      ├─ CPU usage > 80%
                                                                                      ├─ Memory usage > 85%
                                                                                      └─ Security event detected
                                                                                           ↓
                                                                                  Notification (Email, SMS, Dashboard)
                                                                                  ```

                                                                                  ### 8.2 Logging Strategy

                                                                                  ```
                                                                                  All Events Logged:
                                                                                  ├─ User logins & logouts
                                                                                  ├─ API calls & responses
                                                                                  ├─ Data access & modifications
                                                                                  ├─ Error messages & stack traces
                                                                                  ├─ Security events
                                                                                  ├─ System health metrics
                                                                                  └─ Compliance audit trail

                                                                                  Retention:
                                                                                  ├─ Hot (accessible): 30 days
                                                                                  ├─ Warm (archived): 1 year
                                                                                  └─ Cold (compliance): 7 years
                                                                                  ```

                                                                                  ---

                                                                                  ## 9. Development & Deployment

                                                                                  ### 9.1 CI/CD Pipeline

                                                                                  ```
                                                                                  Code Push to GitHub
                                                                                      ↓
                                                                                  [GitHub Actions]
                                                                                      ├─ Run unit tests
                                                                                      ├─ Security scanning
                                                                                      ├─ Code quality checks
                                                                                      └─ Build Docker images
                                                                                           ↓
                                                                                  [Push to Container Registry]
                                                                                      ↓
                                                                                  [Deploy to Cloud Run]
                                                                                      ├─ Dev environment
                                                                                      ├─ Staging environment
                                                                                      └─ Production (with approval)
                                                                                  ```

                                                                                  ### 9.2 Environment Strategy

                                                                                  | Environment | Purpose | Database | Updates |
                                                                                  |-------------|---------|----------|---------|
                                                                                  | **Dev** | Feature development | Separate | Continuous |
                                                                                  | **Staging** | Pre-production testing | Copy of prod | Weekly |
                                                                                  | **Production** | Live system | Main database | Scheduled |

                                                                                  ---

                                                                                  ## 10. Cost Optimization

                                                                                  ### 10.1 Estimated Monthly Costs (SAR)

                                                                                  | Service | Usage | Cost (SAR) |
                                                                                  |---------|-------|-----------|
                                                                                  | Cloud Run | 5M requests | 1,500 |
                                                                                  | Cloud SQL | 500GB storage | 3,080 |
                                                                                  | BigQuery | 1TB analysis | 1,200 |
                                                                                  | Cloud Storage | 200GB | 45 |
                                                                                  | Load Balancer | Standard | 115 |
                                                                                  | Cloud Armor | Standard | 135 |
                                                                                  | Data Transfer | Egress 50GB | 300 |
                                                                                  | Monitoring | Standard | 375 |
                                                                                  | **TOTAL** | | **6,750** |

                                                                                  ### 10.2 Cost Optimization Strategies

                                                                                  - **Scheduled scaling**: Scale down during off-peak hours
                                                                                  - - **Query optimization**: Reduce BigQuery scans
                                                                                    - - **Data compression**: Reduce storage costs
                                                                                      - - **Reserved capacity**: 30% savings for committed use
                                                                                        - - **Spot instances**: For batch processing (if applicable)
                                                                                         
                                                                                          - ---

                                                                                          ## 11. Technology Stack Summary

                                                                                          | Layer | Component | Technology | Version |
                                                                                          |-------|-----------|-----------|---------|
                                                                                          | Frontend | Conversational UI | Dialogflow CX | Latest |
                                                                                          | Frontend | Agent Framework | Vertex AI | Latest |
                                                                                          | Frontend | Web App | React | 18+ |
                                                                                          | Frontend | Mobile | Flutter | 3.16+ |
                                                                                          | Backend | Compute | Cloud Run | Latest |
                                                                                          | Backend | API Framework | Python Flask/FastAPI | Latest |
                                                                                          | Backend | AI Models | Gemini 1.5 | Latest |
                                                                                          | Backend | Task Queue | Cloud Tasks | Latest |
                                                                                          | Backend | Messaging | Pub/Sub | Latest |
                                                                                          | Backend | Caching | Memorystore | 7+ |
                                                                                          | Database | Transactional | PostgreSQL | 15+ |
                                                                                          | Database | Analytics | BigQuery | Latest |
                                                                                          | Database | Storage | Cloud Storage | Latest |
                                                                                          | Security | Encryption Keys | Cloud KMS | Latest |
                                                                                          | Monitoring | Logging | Cloud Logging | Latest |
                                                                                          | Monitoring | Metrics | Cloud Monitoring | Latest |

                                                                                          ---

                                                                                          **Document Classification:** Confidential - Al Jazeerah River Company Internal Use Only
                                                                                          **Last Updated:** January 18, 2026
                                                                                          **Next Review:** April 18, 2026
