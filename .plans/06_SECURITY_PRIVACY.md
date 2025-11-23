# Security & Privacy Plan
## Closed-Loop Diabetes Management System

**Document Version:** 1.0
**Date:** 2025-11-23
**Status:** Draft

---

## Table of Contents
1. [Security Overview](#1-security-overview)
2. [Threat Model](#2-threat-model)
3. [Security Architecture](#3-security-architecture)
4. [Data Protection](#4-data-protection)
5. [Application Security](#5-application-security)
6. [Infrastructure Security](#6-infrastructure-security)
7. [Incident Response](#7-incident-response)
8. [Compliance & Auditing](#8-compliance--auditing)

---

## 1. Security Overview

### 1.1 Security Objectives

**CIA Triad for Medical Devices:**

1. **Confidentiality:** Protect patient health data from unauthorized access
2. **Integrity:** Ensure insulin commands are not tampered with
3. **Availability:** System must be available for life-critical functions

**Additional Medical Device Security Goals:**
- **Safety:** Security failures must not harm patients
- **Authenticity:** Verify identity of users, devices, and commands
- **Non-repudiation:** Audit trail of all critical actions

### 1.2 Security Principles

1. **Defense in Depth:** Multiple layers of security
2. **Least Privilege:** Minimal access rights
3. **Fail Secure:** Security failures default to safe state
4. **Security by Design:** Built-in from the start, not bolted on
5. **Zero Trust:** Never trust, always verify
6. **Privacy by Default:** Maximum privacy settings as default

### 1.3 Compliance Requirements

- **HIPAA Security Rule** (45 CFR Part 164.300)
- **GDPR** (EU 2016/679)
- **FDA Cybersecurity Guidance** (2023)
- **ISO 27001** (Information Security Management)
- **SOC 2 Type II** (Security, Availability, Confidentiality)
- **NIST Cybersecurity Framework**

---

## 2. Threat Model

### 2.1 Assets to Protect

| Asset | Confidentiality | Integrity | Availability |
|-------|----------------|-----------|--------------|
| **Patient Health Data** | Critical | High | Medium |
| **Insulin Delivery Commands** | Low | **Critical** | **Critical** |
| **User Credentials** | **Critical** | Critical | High |
| **ML Models** | Medium | High | High |
| **Software Updates** | Low | **Critical** | Medium |
| **Audit Logs** | High | **Critical** | High |

### 2.2 Threat Actors

**Adversary Profiles:**

1. **Nation-State Actors**
   - Sophistication: Very High
   - Motivation: Espionage, disruption
   - Likelihood: Low
   - Impact: Catastrophic

2. **Cybercriminals**
   - Sophistication: High
   - Motivation: Financial gain (ransomware, data theft)
   - Likelihood: Medium
   - Impact: High

3. **Hacktivists**
   - Sophistication: Medium
   - Motivation: Ideological, publicity
   - Likelihood: Low
   - Impact: Medium

4. **Insiders (Malicious)**
   - Sophistication: Medium
   - Motivation: Various (revenge, financial)
   - Likelihood: Low
   - Impact: High

5. **Script Kiddies**
   - Sophistication: Low
   - Motivation: Curiosity, vandalism
   - Likelihood: Medium
   - Impact: Low

6. **Unintentional Insiders**
   - Sophistication: N/A
   - Motivation: Accidental
   - Likelihood: High
   - Impact: Medium

### 2.3 Attack Scenarios

**Critical Threat Scenarios:**

#### Scenario 1: Insulin Over-Delivery Attack
```
Attacker Goal: Cause hypoglycemia by commanding excessive insulin

Attack Vector:
  1. Man-in-the-middle attack on mobile app ↔ backend communication
  2. Intercept insulin command
  3. Modify to increase dose
  4. Send to pump

Impact: Life-threatening hypoglycemia

Mitigations:
  ✓ End-to-end encryption (TLS 1.3)
  ✓ Certificate pinning in mobile app
  ✓ Message authentication codes (HMAC)
  ✓ Pump-side validation of commands
  ✓ Maximum insulin limits enforced at multiple layers
```

#### Scenario 2: Ransomware Attack
```
Attacker Goal: Encrypt patient data, demand ransom

Attack Vector:
  1. Phishing email to employee
  2. Install malware on internal network
  3. Lateral movement to database servers
  4. Encrypt patient data

Impact: Loss of access to patient data, service disruption

Mitigations:
  ✓ Network segmentation
  ✓ Regular backups (air-gapped)
  ✓ Endpoint detection and response (EDR)
  ✓ Security awareness training
  ✓ Multi-factor authentication (MFA)
```

#### Scenario 3: Data Breach
```
Attacker Goal: Steal patient health records for sale

Attack Vector:
  1. SQL injection vulnerability in API
  2. Extract patient data
  3. Sell on dark web

Impact: HIPAA violation, patient privacy breach, reputational damage

Mitigations:
  ✓ Input validation (Pydantic)
  ✓ Parameterized queries (SQLAlchemy ORM)
  ✓ Web Application Firewall (WAF)
  ✓ Database encryption at rest
  ✓ Field-level encryption for PHI
  ✓ Regular penetration testing
```

#### Scenario 4: Denial of Service (DoS)
```
Attacker Goal: Make system unavailable

Attack Vector:
  1. DDoS attack on API endpoints
  2. Overwhelm servers

Impact: Users unable to access system (but closed-loop continues offline)

Mitigations:
  ✓ DDoS protection (Cloudflare, AWS Shield)
  ✓ Rate limiting
  ✓ Auto-scaling
  ✓ Offline mode in mobile app (critical!)
```

#### Scenario 5: Compromised ML Model
```
Attacker Goal: Manipulate glucose predictions

Attack Vector:
  1. Poison training data
  2. Model makes incorrect predictions
  3. Users receive incorrect insulin doses

Impact: Poor glycemic control, potential harm

Mitigations:
  ✓ Data validation and anomaly detection
  ✓ Model monitoring for drift
  ✓ A/B testing before full deployment
  ✓ Classical control as safety layer (doesn't rely on ML)
```

### 2.4 STRIDE Analysis

| Threat Type | Example | Mitigation |
|-------------|---------|------------|
| **Spoofing** | Attacker impersonates user | MFA, certificate pinning |
| **Tampering** | Modify insulin commands | HMAC, TLS, code signing |
| **Repudiation** | User denies action | Audit logging, non-repudiation |
| **Information Disclosure** | Data breach | Encryption, access control |
| **Denial of Service** | DDoS attack | Rate limiting, DDoS protection |
| **Elevation of Privilege** | Attacker gains admin access | RBAC, least privilege |

---

## 3. Security Architecture

### 3.1 Network Security

**Network Segmentation:**

```
Internet
  ↓
[Cloudflare / AWS Shield] ← DDoS Protection
  ↓
[Web Application Firewall (WAF)]
  ↓
[Application Load Balancer] ← SSL/TLS Termination
  ↓
┌──────────────────────────────────────┐
│        DMZ (Public Subnet)            │
│  - API Gateway                        │
│  - No direct DB access                │
└──────────────────────────────────────┘
  ↓ (controlled traffic only)
┌──────────────────────────────────────┐
│      Application Tier                 │
│      (Private Subnet)                 │
│  - Backend services                   │
│  - No internet access                 │
│  - NAT Gateway for outbound only      │
└──────────────────────────────────────┘
  ↓
┌──────────────────────────────────────┐
│        Data Tier                      │
│      (Private Subnet)                 │
│  - Databases                          │
│  - No internet access                 │
│  - Isolated from application tier     │
└──────────────────────────────────────┘
```

**Firewall Rules:**
- Deny all by default
- Allow only necessary ports (443, 5432, 6379)
- Whitelist IP ranges
- Stateful inspection

**Network Monitoring:**
- VPC Flow Logs (all traffic)
- Intrusion Detection System (IDS)
- Anomaly detection (ML-based)

### 3.2 Identity & Access Management

**Authentication:**

```python
# Multi-factor authentication required
class AuthenticationService:
    def login(self, email, password):
        # Step 1: Validate credentials
        user = self.validate_credentials(email, password)

        # Step 2: Require MFA (unless remembered device)
        if not self.is_remembered_device(request.device_id):
            mfa_code = self.send_mfa_code(user.phone)
            self.wait_for_mfa_verification()

        # Step 3: Generate tokens
        access_token = self.create_jwt(user, expires_in=15*60)  # 15 min
        refresh_token = self.create_jwt(user, expires_in=30*24*3600)  # 30 days

        # Step 4: Audit log
        self.log_login(user.id, request.ip_address)

        return access_token, refresh_token
```

**Password Policy:**
- Minimum 12 characters
- Must include: uppercase, lowercase, number, special char
- No common passwords (check against breached password database)
- Hashed with Argon2id (not bcrypt or SHA)
- Salted (unique per user)

**Role-Based Access Control (RBAC):**

| Role | Permissions |
|------|-------------|
| **Patient** | Read own data, update settings, log meals |
| **Caregiver** | Read patient data (with consent), receive alerts |
| **Clinician** | Read patient data (with consent), view reports |
| **Support** | Read limited data for troubleshooting |
| **Admin** | User management, system configuration |
| **Data Scientist** | Read de-identified data only |

**API Authentication:**
- OAuth 2.0 / OpenID Connect
- JWT tokens with short expiration
- Token refresh mechanism
- Token revocation support

### 3.3 Mobile App Security

**iOS Security:**
- Keychain for credential storage
- Biometric authentication (Face ID / Touch ID)
- Certificate pinning (prevent MITM)
- Code obfuscation (anti-reverse engineering)
- Jailbreak detection
- Local database encryption (Core Data encryption)

**Android Security:**
- EncryptedSharedPreferences for credentials
- Biometric authentication (BiometricPrompt API)
- Certificate pinning
- ProGuard / R8 obfuscation
- Root detection
- SafetyNet Attestation
- Room database encryption

**Bluetooth Security:**
- Pairing with PIN/passkey
- Encrypted Bluetooth LE communication
- Device authentication
- Session keys (rotated)

**App Hardening:**
```
Security Checklist:
☑ SSL/TLS certificate pinning
☑ Disable debugging in production builds
☑ Obfuscate code
☑ Secure storage (Keychain/EncryptedSharedPreferences)
☑ Biometric authentication
☑ Jailbreak/root detection
☑ Runtime application self-protection (RASP)
☑ Screen capture prevention (for sensitive screens)
☑ Clipboard protection
```

---

## 4. Data Protection

### 4.1 Encryption

**Data in Transit:**
- **TLS 1.3** for all HTTPS communication
- **Certificate Pinning** in mobile apps
- **Perfect Forward Secrecy** (ephemeral keys)
- **Bluetooth LE Encryption** for device communication

**Data at Rest:**
- **Database Encryption:** AES-256 (PostgreSQL/TimescaleDB transparent encryption)
- **S3 Bucket Encryption:** SSE-KMS (AWS Key Management Service)
- **Mobile Device Encryption:**
  - iOS: Data Protection API (automatic with device passcode)
  - Android: File-based encryption
- **Backup Encryption:** Encrypted backups (AES-256)

**Field-Level Encryption (Extra Protection for PHI):**
```python
# Sensitive fields encrypted with separate keys
class EncryptedUserModel:
    id = Column(UUID)
    email = Column(String)  # Not encrypted (needed for lookup)

    # Encrypted fields
    full_name = Column(EncryptedString)  # AES-256-GCM
    date_of_birth = Column(EncryptedDate)
    ssn = Column(EncryptedString)  # If collected

    # Encryption keys stored in AWS KMS
    # Separate key per tenant for key isolation
```

**Key Management:**
- **AWS KMS** (Key Management Service)
- **Key Rotation:** Automatic annual rotation
- **Key Hierarchy:**
  - Master key (KMS, never leaves AWS)
  - Data encryption keys (DEK) for each tenant
  - Envelope encryption
- **Key Access Control:** IAM policies, audit logging

### 4.2 Data Minimization

**Collect Only What's Necessary:**
- Don't collect SSN (unless required for billing)
- Don't collect unnecessary demographics
- Anonymous analytics (no PII in analytics)

**Data Retention:**
- Delete old data per retention policy (see Regulatory doc)
- User-initiated deletion (GDPR "right to be forgotten")
- Automatic data archival

**De-identification for Research:**
```python
def anonymize_for_research(data):
    """
    HIPAA Safe Harbor de-identification
    Remove 18 identifiers:
    """
    anonymized = data.copy()

    # Remove direct identifiers
    del anonymized['name']
    del anonymized['email']
    del anonymized['phone']
    del anonymized['address']
    del anonymized['ssn']
    del anonymized['device_id']

    # Generalize dates
    anonymized['diagnosis_date'] = anonymized['diagnosis_date'].year  # Year only

    # Generalize location
    anonymized['zip_code'] = anonymized['zip_code'][:3] + "00"  # First 3 digits only

    # Assign pseudonymous ID
    anonymized['research_id'] = generate_uuid()

    return anonymized
```

### 4.3 Data Loss Prevention (DLP)

**Prevent Data Exfiltration:**
- **Database Activity Monitoring:** Alert on bulk exports
- **API Rate Limiting:** Prevent rapid data extraction
- **Access Logging:** All data access logged
- **Anomaly Detection:** ML-based detection of unusual access patterns

**Example Alert:**
```
Alert: User 'data_scientist_123' downloaded data for 1000+ users
Action: Automatically block, notify security team, require justification
```

---

## 5. Application Security

### 5.1 Secure Development Lifecycle (SDL)

**Security in Every Phase:**

```
1. Requirements
   - Security requirements gathering
   - Threat modeling

2. Design
   - Security architecture review
   - Privacy impact assessment

3. Implementation
   - Secure coding guidelines
   - Code reviews (security-focused)
   - Static analysis (SAST)

4. Testing
   - Dynamic analysis (DAST)
   - Penetration testing
   - Fuzz testing

5. Deployment
   - Security hardening
   - Secrets management
   - Infrastructure as Code review

6. Operations
   - Security monitoring
   - Incident response
   - Patch management
```

### 5.2 Secure Coding Practices

**Input Validation:**
```python
# Always validate and sanitize inputs
from pydantic import BaseModel, validator, Field

class GlucoseReadingInput(BaseModel):
    value: int = Field(..., ge=40, le=400)  # Physiological range
    timestamp: datetime

    @validator('value')
    def validate_glucose(cls, v):
        if v < 40 or v > 400:
            raise ValueError('Glucose value out of valid range')
        return v

    @validator('timestamp')
    def validate_timestamp(cls, v):
        # Prevent time-based attacks
        if v > datetime.utcnow() + timedelta(minutes=5):
            raise ValueError('Timestamp in future')
        if v < datetime.utcnow() - timedelta(days=7):
            raise ValueError('Timestamp too old')
        return v
```

**SQL Injection Prevention:**
```python
# ALWAYS use ORM or parameterized queries
# GOOD:
user = db.query(User).filter(User.email == email).first()

# BAD (never do this):
# query = f"SELECT * FROM users WHERE email = '{email}'"
```

**XSS Prevention:**
- Escape all user-generated content
- Content Security Policy (CSP) headers
- HttpOnly cookies

**CSRF Prevention:**
- CSRF tokens for state-changing requests
- SameSite cookies

### 5.3 Dependency Management

**Vulnerability Scanning:**
```bash
# Automated scanning in CI/CD pipeline
pip install safety
safety check  # Check for known vulnerabilities

# Alternatively
pip install pip-audit
pip-audit
```

**Dependency Updates:**
- Automated dependency updates (Dependabot, Renovate)
- Review and test before merging
- Pin versions (requirements.txt with exact versions)

**Software Bill of Materials (SBOM):**
- Document all dependencies
- Track CVEs
- FDA requirement for medical devices

### 5.4 API Security

**Rate Limiting:**
```python
from fastapi import Request
from fastapi_limiter import RateLimiter

@app.post("/api/v1/data/glucose")
@limiter.limit("100/minute")  # Max 100 requests per minute
async def create_glucose_reading(request: Request, data: GlucoseReadingCreate):
    pass
```

**API Gateway Security:**
- Authentication required for all endpoints
- Authorization checks (RBAC)
- Request validation (schema validation)
- Response size limits
- Timeout limits

**API Versioning:**
- Version all APIs (e.g., /api/v1/)
- Deprecation policy (6 months notice)
- Security patches for old versions

---

## 6. Infrastructure Security

### 6.1 Cloud Security (AWS)

**Security Best Practices:**

**IAM (Identity and Access Management):**
- Principle of least privilege
- No root account usage
- MFA for all users
- Service roles (EC2, Lambda) instead of API keys
- Regularly audit permissions

**VPC Configuration:**
- Private subnets for all services
- Public subnets only for load balancers
- NAT Gateway for outbound internet (updates)
- VPC Flow Logs enabled

**Security Groups & NACLs:**
- Deny all by default
- Explicitly allow necessary traffic
- No 0.0.0.0/0 for SSH (use bastion host or AWS Systems Manager)

**AWS Security Services:**
- **GuardDuty:** Threat detection
- **Security Hub:** Centralized security findings
- **Config:** Configuration compliance monitoring
- **CloudTrail:** Audit logging (all API calls)
- **Secrets Manager:** Credentials management
- **KMS:** Key management

### 6.2 Container Security

**Docker Security:**
```dockerfile
# Use minimal base images
FROM python:3.12-slim

# Don't run as root
RUN useradd -m -u 1000 appuser
USER appuser

# Copy only necessary files
COPY --chown=appuser:appuser requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Read-only filesystem (where possible)
```

**Kubernetes Security:**
- **Network Policies:** Restrict pod-to-pod communication
- **RBAC:** Role-based access for service accounts
- **Pod Security Policies:** Prevent privileged containers
- **Secrets Management:** Use Kubernetes Secrets (encrypted at rest)
- **Image Scanning:** Scan all images for vulnerabilities

**Container Image Scanning:**
```bash
# Scan images in CI/CD
trivy image myapp:latest
# Fail build if critical vulnerabilities found
```

### 6.3 Secrets Management

**Never Hardcode Secrets:**
```python
# BAD:
# DATABASE_URL = "postgresql://user:password@localhost/db"

# GOOD:
from src.core.config import settings  # Loads from environment
DATABASE_URL = settings.DATABASE_URL
```

**Environment-Specific Secrets:**
- Development: .env file (not committed to git)
- Staging/Production: AWS Secrets Manager or HashiCorp Vault

**Secret Rotation:**
- Automatic rotation every 90 days
- Database credentials
- API keys
- Encryption keys

---

## 7. Incident Response

### 7.1 Incident Response Plan

**Phases:**

```
1. Preparation
   - Incident response team
   - Playbooks for common scenarios
   - Tools and access ready

2. Detection & Analysis
   - Security alerts (SIEM)
   - Triage incident
   - Determine severity

3. Containment
   - Short-term: Isolate affected systems
   - Long-term: Patch vulnerabilities

4. Eradication
   - Remove malware/attacker access
   - Fix root cause

5. Recovery
   - Restore systems
   - Monitor for re-infection

6. Post-Incident
   - Root cause analysis
   - Lessons learned
   - Update defenses
```

### 7.2 Incident Response Team

| Role | Responsibility |
|------|----------------|
| **Incident Commander** | Overall coordination |
| **Security Lead** | Technical investigation |
| **Engineering Lead** | System remediation |
| **Legal Counsel** | Legal implications, breach notification |
| **Regulatory Affairs** | FDA/regulatory reporting |
| **Communications** | Internal/external communication |
| **Customer Support** | User communication |

### 7.3 Breach Notification

**HIPAA Breach Notification:**

**Timeline:**
- **Discover breach** → Investigate (within 5 days)
- **Confirm breach** → Notify individuals (within 60 days)
- **>500 affected** → Notify HHS and media immediately
- **<500 affected** → Annual report to HHS

**Notification Contents:**
- Description of breach
- Types of information compromised
- Steps individuals should take
- What organization is doing
- Contact information

**Example Breach Scenarios:**

| Scenario | HIPAA Breach? | Action Required |
|----------|---------------|-----------------|
| Laptop with encrypted data stolen | No (encrypted) | Assess, document |
| Laptop with unencrypted PHI stolen | **Yes** | Notify individuals, HHS |
| Employee emails PHI to wrong person | **Yes** (if >limited data set) | Notify, retrain |
| Database exposed on internet for 1 hour | **Yes** | Investigate, notify if accessed |

### 7.4 Security Monitoring

**SIEM (Security Information and Event Management):**
- Centralized logging (Elasticsearch, Splunk, or Datadog)
- Real-time alerts
- Correlation rules
- Dashboards

**Key Metrics to Monitor:**
- Failed login attempts
- Unusual data access patterns
- API rate limit violations
- Privilege escalation attempts
- Database export operations
- Software update installations

**Alerting:**
```python
# Example: Alert on suspicious activity
def monitor_login_attempts():
    failed_attempts = get_failed_logins(user_id, last_15_minutes)

    if failed_attempts > 5:
        # Potential brute force attack
        lock_account(user_id)
        alert_security_team(f"Account {user_id} locked due to {failed_attempts} failed logins")

def monitor_data_export():
    rows_exported = get_export_count(user_id, last_hour)

    if rows_exported > 10000:
        # Potential data exfiltration
        alert_security_team(f"User {user_id} exported {rows_exported} rows")
        require_justification(user_id)
```

---

## 8. Compliance & Auditing

### 8.1 Security Audits

**Internal Audits:**
- **Frequency:** Quarterly
- **Scope:** Review access logs, security controls, code changes
- **Team:** Internal security team

**External Audits:**
- **SOC 2 Type II:** Annual audit by third-party
- **Penetration Testing:** Bi-annual external pentest
- **HIPAA Audit:** Annual HIPAA compliance review

### 8.2 Penetration Testing

**Scope:**
- Web application security (API, mobile backends)
- Mobile app security (iOS, Android)
- Network security
- Physical security (if applicable)
- Social engineering (phishing tests)

**Methodology:**
- OWASP Testing Guide
- OWASP Mobile Security Testing Guide
- NIST SP 800-115

**Remediation:**
- Critical: 7 days
- High: 30 days
- Medium: 90 days
- Low: Next release

### 8.3 Compliance Certifications

**Target Certifications:**

| Certification | Timeline | Effort |
|--------------|----------|--------|
| **SOC 2 Type II** | Month 18 | High |
| **ISO 27001** | Month 24 | High |
| **HITRUST CSF** | Month 30 | Very High |

**Why Each Matters:**
- **SOC 2:** Required by enterprise customers
- **ISO 27001:** International standard, EU requirement
- **HITRUST CSF:** Healthcare-specific, comprehensive framework

---

## Appendix A: Security Checklist

**Launch Readiness Security Checklist:**

**Authentication & Authorization:**
- [ ] MFA enabled for all users
- [ ] Strong password policy enforced
- [ ] Argon2id password hashing
- [ ] RBAC implemented and tested
- [ ] Session timeout configured

**Data Protection:**
- [ ] TLS 1.3 for all communications
- [ ] Certificate pinning in mobile apps
- [ ] Database encryption at rest
- [ ] Field-level encryption for sensitive PHI
- [ ] Encryption keys managed in KMS

**Application Security:**
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention (ORM)
- [ ] XSS prevention (output escaping)
- [ ] CSRF tokens implemented
- [ ] Rate limiting configured
- [ ] SAST/DAST tools integrated in CI/CD

**Infrastructure:**
- [ ] Network segmentation in place
- [ ] Firewalls configured (deny by default)
- [ ] DDoS protection enabled
- [ ] Intrusion detection system active
- [ ] Secrets stored in Secrets Manager (not code)

**Monitoring & Response:**
- [ ] SIEM configured and monitored
- [ ] Security alerts defined
- [ ] Incident response plan documented
- [ ] Incident response team identified
- [ ] Breach notification process ready

**Compliance:**
- [ ] HIPAA Security Rule compliance verified
- [ ] GDPR compliance verified (if EU users)
- [ ] SOC 2 Type II certification obtained
- [ ] Penetration test completed
- [ ] Security audit passed

---

**Document Approval:**
- Chief Security Officer: _________________
- VP Engineering: _________________
- Compliance Officer: _________________
- Privacy Officer: _________________

**Change Log:**
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-23 | Initial | First draft |
