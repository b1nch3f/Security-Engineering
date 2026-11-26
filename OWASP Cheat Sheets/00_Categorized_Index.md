# OWASP Cheat Sheet Series — Categorized Index

All entries crawled from the OWASP Cheat Sheet Series glossary index
(`https://cheatsheetseries.owasp.org/Glossary.html`) on 2026-11-25, then grouped by
security domain.

Base URL for every entry: `https://cheatsheetseries.owasp.org/cheatsheets/`

| Category | Count |
|---|---|
| [1. Authentication & Identity](#1-authentication--identity) | 11 |
| [2. Authorization & Access Control](#2-authorization--access-control) | 8 |
| [3. Sessions, Cookies & Tokens](#3-sessions-cookies--tokens) | 3 |
| [4. Input Validation & Injection](#4-input-validation--injection) | 18 |
| [5. Browser & Client-Side Security](#5-browser--client-side-security) | 15 |
| [6. APIs, Protocols & Web Architecture](#6-apis-protocols--web-architecture) | 10 |
| [7. Cryptography, TLS & Secrets](#7-cryptography-tls--secrets) | 10 |
| [8. Data Storage, Privacy & Parsing](#8-data-storage-privacy--parsing) | 6 |
| [9. Cloud, Containers & Infrastructure](#9-cloud-containers--infrastructure) | 10 |
| [10. CI/CD & Software Supply Chain](#10-cicd--software-supply-chain) | 7 |
| [11. AI, LLM & Agent Security](#11-ai-llm--agent-security) | 8 |
| [12. Language & Framework Hardening](#12-language--framework-hardening) | 17 |
| [13. Secure Design, Threat Modeling & Review](#13-secure-design-threat-modeling--review) | 7 |
| [14. Operations, Monitoring & Response](#14-operations-monitoring--response) | 6 |
| [15. Platforms & Embedded Systems](#15-platforms--embedded-systems) | 3 |
| [16. General Reference](#16-general-reference) | 1 |
| **Total** | **140** |

> Sensible starting points: category 13 for greenfield design work, categories 1–8 for
> application-level defence, 9–11 for platform roles, 14 for run-time operations.

---

## 1. Authentication & Identity

Confirming *who* a subject is, and how identity travels between components.

- [Authentication Cheat Sheet](Authentication_Cheat_Sheet.html)
- [Authentication Patterns Cheat Sheet](Authentication_Patterns_Cheat_Sheet.html)
- [Choosing and Using Security Questions Cheat Sheet](Choosing_and_Using_Security_Questions_Cheat_Sheet.html)
- [Credential Stuffing Prevention Cheat Sheet](Credential_Stuffing_Prevention_Cheat_Sheet.html)
- [Forgot Password Cheat Sheet](Forgot_Password_Cheat_Sheet.html)
- [Multifactor Authentication Cheat Sheet](Multifactor_Authentication_Cheat_Sheet.html)
- [Passkey Security Cheat Sheet](Passkey_Security_Cheat_Sheet.html)
- [OAuth2 Cheat Sheet](OAuth2_Cheat_Sheet.html)
- [SAML Security Cheat Sheet](SAML_Security_Cheat_Sheet.html)
- [Identity Propagation Patterns Cheat Sheet](Identity_Propagation_Patterns_Cheat_Sheet.html)
- [Workload Identity Federation Cheat Sheet](Workload_Identity_Federation_Cheat_Sheet.html)

## 2. Authorization & Access Control

Confirming *what* an authenticated subject may do — including the testing that proves it.

- [Access Control Cheat Sheet](Access_Control_Cheat_Sheet.html)
- [Authorization Cheat Sheet](Authorization_Cheat_Sheet.html)
- [Authorization Patterns Cheat Sheet](Authorization_Patterns_Cheat_Sheet.html)
- [Authorization Decisions And Output Handling Cheat Sheet](Authorization_Decisions_And_Output_Handling_Cheat_Sheet.html)
- [Authorization Policy And Data Distribution Cheat Sheet](Authorization_Policy_And_Data_Distribution_Cheat_Sheet.html)
- [Authorization Regression Testing Cheat Sheet](Authorization_Regression_Testing_Cheat_Sheet.html)
- [Authorization Testing Automation Cheat Sheet](Authorization_Testing_Automation_Cheat_Sheet.html)
- [Insecure Direct Object Reference Prevention Cheat Sheet](Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)

## 3. Sessions, Cookies & Tokens

Lifecycle, transport and theft-resistance of session state.

- [Session Management Cheat Sheet](Session_Management_Cheat_Sheet.html)
- [Cookie Theft Mitigation Cheat Sheet](Cookie_Theft_Mitigation_Cheat_Sheet.html)
- [JSON Web Token Cheat Sheet](JSON_Web_Token_Cheat_Sheet.html)

## 4. Input Validation & Injection

Untrusted data reaching an interpreter — SQL, shell, LDAP, XPath, templates, deserialisers,
parsers and filesystem paths.

- [Input Validation Cheat Sheet](Input_Validation_Cheat_Sheet.html)
- [Injection Prevention Cheat Sheet](Injection_Prevention_Cheat_Sheet.html)
- [Injection Prevention in Java Cheat Sheet](Injection_Prevention_in_Java_Cheat_Sheet.html)
- [SQL Injection Prevention Cheat Sheet](SQL_Injection_Prevention_Cheat_Sheet.html)
- [Query Parameterization Cheat Sheet](Query_Parameterization_Cheat_Sheet.html)
- [NoSQL Security Cheat Sheet](NoSQL_Security_Cheat_Sheet.html)
- [LDAP Injection Prevention Cheat Sheet](LDAP_Injection_Prevention_Cheat_Sheet.html)
- [XPath Injection Prevention Cheat Sheet](XPath_Injection_Prevention_Cheat_Sheet.html)
- [OS Command Injection Defense Cheat Sheet](OS_Command_Injection_Defense_Cheat_Sheet.html)
- [Server Side Template Injection Prevention Cheat Sheet](Server_Side_Template_Injection_Prevention_Cheat_Sheet.html)
- [Server Side Request Forgery Prevention Cheat Sheet](Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
- [Path Traversal Prevention Cheat Sheet](Path_Traversal_Prevention_Cheat_Sheet.html)
- [File Upload Cheat Sheet](File_Upload_Cheat_Sheet.html)
- [Deserialization Cheat Sheet](Deserialization_Cheat_Sheet.html)
- [Mass Assignment Cheat Sheet](Mass_Assignment_Cheat_Sheet.html)
- [Prototype Pollution Prevention Cheat Sheet](Prototype_Pollution_Prevention_Cheat_Sheet.html)
- [Email Validation and Verification Cheat Sheet](Email_Validation_and_Verification_Cheat_Sheet.html)
- [Unvalidated Redirects and Forwards Cheat Sheet](Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html)

## 5. Browser & Client-Side Security

Execution of untrusted content in the browser, and the headers/policies that contain it.

- [Cross Site Scripting Prevention Cheat Sheet](Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [DOM based XSS Prevention Cheat Sheet](DOM_based_XSS_Prevention_Cheat_Sheet.html)
- [XSS Filter Evasion Cheat Sheet](XSS_Filter_Evasion_Cheat_Sheet.html)
- [DOM Clobbering Prevention Cheat Sheet](DOM_Clobbering_Prevention_Cheat_Sheet.html)
- [Content Security Policy Cheat Sheet](Content_Security_Policy_Cheat_Sheet.html)
- [Cross-Site Request Forgery Prevention Cheat Sheet](Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [CORS Security Cheat Sheet](CORS_Security_Cheat_Sheet.html)
- [Clickjacking Defense Cheat Sheet](Clickjacking_Defense_Cheat_Sheet.html)
- [XS Leaks Cheat Sheet](XS_Leaks_Cheat_Sheet.html)
- [HTML5 Security Cheat Sheet](HTML5_Security_Cheat_Sheet.html)
- [Web Frontend Security Cheat Sheet](Web_Frontend_Security_Cheat_Sheet.html)
- [Micro Frontend Security Cheat Sheet](Micro_Frontend_Security_Cheat_Sheet.html)
- [Third Party Javascript Management Cheat Sheet](Third_Party_Javascript_Management_Cheat_Sheet.html)
- [Browser Extension Vulnerabilities Cheat Sheet](Browser_Extension_Vulnerabilities_Cheat_Sheet.html)
- [Bot Management and Anti-Automation Cheat Sheet](Bot_Management_and_Anti-Automation_Cheat_Sheet.html)

## 6. APIs, Protocols & Web Architecture

Service-to-service contracts, transports and the request-routing layer.

- [REST Security Cheat Sheet](REST_Security_Cheat_Sheet.html)
- [REST Assessment Cheat Sheet](REST_Assessment_Cheat_Sheet.html)
- [GraphQL Cheat Sheet](GraphQL_Cheat_Sheet.html)
- [gRPC Security Cheat Sheet](gRPC_Security_Cheat_Sheet.html)
- [Web Service Security Cheat Sheet](Web_Service_Security_Cheat_Sheet.html)
- [WebSocket Security Cheat Sheet](WebSocket_Security_Cheat_Sheet.html)
- [Webhook Security Cheat Sheet](Webhook_Security_Cheat_Sheet.html)
- [HTTP Headers Cheat Sheet](HTTP_Headers_Cheat_Sheet.html)
- [Microservices Security Cheat Sheet](Microservices_Security_Cheat_Sheet.html)
- [Microservices based Security Arch Doc Cheat Sheet](Microservices_based_Security_Arch_Doc_Cheat_Sheet.html)

## 7. Cryptography, TLS & Secrets

Algorithms, key material, transport encryption and credential storage.

- [Cryptographic Storage Cheat Sheet](Cryptographic_Storage_Cheat_Sheet.html)
- [Password Storage Cheat Sheet](Password_Storage_Cheat_Sheet.html)
- [Key Management Cheat Sheet](Key_Management_Cheat_Sheet.html)
- [Secrets Management Cheat Sheet](Secrets_Management_Cheat_Sheet.html)
- [Transport Layer Protection Cheat Sheet](Transport_Layer_Protection_Cheat_Sheet.html)
- [Transport Layer Security Cheat Sheet](Transport_Layer_Security_Cheat_Sheet.html)
- [TLS Cipher String Cheat Sheet](TLS_Cipher_String_Cheat_Sheet.html)
- [Pinning Cheat Sheet](Pinning_Cheat_Sheet.html)
- [Post Quantum Cryptography Cheat Sheet](Post_Quantum_Cryptography_Cheat_Sheet.html)
- [HTTP Strict Transport Security Cheat Sheet](HTTP_Strict_Transport_Security_Cheat_Sheet.html)

## 8. Data Storage, Privacy & Parsing

Persistence layers, document formats and personal-data handling.

- [Database Security Cheat Sheet](Database_Security_Cheat_Sheet.html)
- [XML Security Cheat Sheet](XML_Security_Cheat_Sheet.html)
- [XML External Entity Prevention Cheat Sheet](XML_External_Entity_Prevention_Cheat_Sheet.html)
- [User Privacy Protection Cheat Sheet](User_Privacy_Protection_Cheat_Sheet.html)
- [Multi Tenant Security Cheat Sheet](Multi_Tenant_Security_Cheat_Sheet.html)
- [Zero Trust Architecture Cheat Sheet](Zero_Trust_Architecture_Cheat_Sheet.html)

## 9. Cloud, Containers & Infrastructure

Runtime platforms, orchestration, network boundaries and declarative infrastructure.

- [Secure Cloud Architecture Cheat Sheet](Secure_Cloud_Architecture_Cheat_Sheet.html)
- [Docker Security Cheat Sheet](Docker_Security_Cheat_Sheet.html)
- [NodeJS Docker Cheat Sheet](NodeJS_Docker_Cheat_Sheet.html)
- [Kubernetes Security Cheat Sheet](Kubernetes_Security_Cheat_Sheet.html)
- [Serverless FaaS Security Cheat Sheet](Serverless_FaaS_Security_Cheat_Sheet.html)
- [Infrastructure as Code Security Cheat Sheet](Infrastructure_as_Code_Security_Cheat_Sheet.html)
- [Network Segmentation Cheat Sheet](Network_Segmentation_Cheat_Sheet.html)
- [Subdomain Takeover Prevention Cheat Sheet](Subdomain_Takeover_Prevention_Cheat_Sheet.html)
- [Denial of Service Cheat Sheet](Denial_of_Service_Cheat_Sheet.html)
- [Web Cache Security Cheat Sheet](Web_Cache_Security_Cheat_Sheet.html)

## 10. CI/CD & Software Supply Chain

Build pipelines, package ecosystems and dependency provenance.

- [CI CD Security Cheat Sheet](CI_CD_Security_Cheat_Sheet.html)
- [GitHub Actions Security Cheat Sheet](GitHub_Actions_Security_Cheat_Sheet.html)
- [Software Supply Chain Security Cheat Sheet](Software_Supply_Chain_Security_Cheat_Sheet.html)
- [Dependency Graph SBOM Cheat Sheet](Dependency_Graph_SBOM_Cheat_Sheet.html)
- [Vulnerable Dependency Management Cheat Sheet](Vulnerable_Dependency_Management_Cheat_Sheet.html)
- [NPM Security Cheat Sheet](NPM_Security_Cheat_Sheet.html)
- [Bean Validation Cheat Sheet](Bean_Validation_Cheat_Sheet.html)

## 11. AI, LLM & Agent Security

Model consumption, retrieval pipelines, autonomous agents and their proofs.

- [AI Agent Security Cheat Sheet](AI_Agent_Security_Cheat_Sheet.html)
- [AI-Powered Advertising Systems Security Cheat Sheet](AI-Powered_Advertising_Systems_Security_Cheat_Sheet.html)
- [LLM Prompt Injection Prevention Cheat Sheet](LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
- [RAG Security Cheat Sheet](RAG_Security_Cheat_Sheet.html)
- [MCP Security Cheat Sheet](MCP_Security_Cheat_Sheet.html)
- [Secure AI Model Ops Cheat Sheet](Secure_AI_Model_Ops_Cheat_Sheet.html)
- [AML Sanctions AI Agent Payments Cheat Sheet](AML_Sanctions_AI_Agent_Payments_Cheat_Sheet.html)
- [Verifying Third Party Agent Execution Evidence Cheat Sheet](Verifying_Third_Party_Agent_Execution_Evidence_Cheat_Sheet.html)

## 12. Language & Framework Hardening

Stack-specific defaults, configuration and idioms.

- [Java Security Cheat Sheet](Java_Security_Cheat_Sheet.html)
- [JAAS Cheat Sheet](JAAS_Cheat_Sheet.html)
- [JavaScript and TypeScript Security Cheat Sheet](JavaScript_and_TypeScript_Security_Cheat_Sheet.html)
- [Nodejs Security Cheat Sheet](Nodejs_Security_Cheat_Sheet.html)
- [C-Based Toolchain Hardening Cheat Sheet](C-Based_Toolchain_Hardening_Cheat_Sheet.html)
- [DotNet Security Cheat Sheet](DotNet_Security_Cheat_Sheet.html)
- [PHP Configuration Cheat Sheet](PHP_Configuration_Cheat_Sheet.html)
- [Django Security Cheat Sheet](Django_Security_Cheat_Sheet.html)
- [Django REST Framework Cheat Sheet](Django_REST_Framework_Cheat_Sheet.html)
- [FastAPI Security Cheat Sheet](FastAPI_Security_Cheat_Sheet.html)
- [Laravel Cheat Sheet](Laravel_Cheat_Sheet.html)
- [Symfony Cheat Sheet](Symfony_Cheat_Sheet.html)
- [Ruby on Rails Cheat Sheet](Ruby_on_Rails_Cheat_Sheet.html)
- [React Security Cheat Sheet](React_Security_Cheat_Sheet.html)
- [Nextjs Security Cheat Sheet](Nextjs_Security_Cheat_Sheet.html)
- [Secure Coding with AI Cheat Sheet](Secure_Coding_with_AI_Cheat_Sheet.html)
- [Secure Code Review Cheat Sheet](Secure_Code_Review_Cheat_Sheet.html)

## 13. Secure Design, Threat Modeling & Review

Work done before code exists: scoping, abuse cases, product design.

- [Threat Modeling Cheat Sheet](Threat_Modeling_Cheat_Sheet.html)
- [Attack Surface Analysis Cheat Sheet](Attack_Surface_Analysis_Cheat_Sheet.html)
- [Abuse Case Cheat Sheet](Abuse_Case_Cheat_Sheet.html)
- [Secure Product Design Cheat Sheet](Secure_Product_Design_Cheat_Sheet.html)
- [Business Logic Security Cheat Sheet](Business_Logic_Security_Cheat_Sheet.html)
- [Transaction Authorization Cheat Sheet](Transaction_Authoization_Cheat_Sheet.html)
- [Third Party Payment Gateway Integration Cheat Sheet](Third_Party_Payment_Gateway_Integration_Cheat_Sheet.html)

## 14. Operations, Monitoring & Response

Telemetry, error surfaces and the post-deployment lifecycle.

- [Logging Cheat Sheet](Logging_Cheat_Sheet.html)
- [Logging Vocabulary Cheat Sheet](Logging_Vocabulary_Cheat_Sheet.html)
- [Error Handling Cheat Sheet](Error_Handling_Cheat_Sheet.html)
- [Virtual Patching Cheat Sheet](Virtual_Patching_Cheat_Sheet.html)
- [Legacy Application Management Cheat Sheet](Legacy_Application_Management_Cheat_Sheet.html)
- [Vulnerability Disclosure Cheat Sheet](Vulnerability_Disclosure_Cheat_Sheet.html)

## 15. Platforms & Embedded Systems

Domains outside the browser/server pair.

- [Mobile Application Security Cheat Sheet](Mobile_Application_Security_Cheat_Sheet.html)
- [Automotive Security Cheat Sheet](Automotive_Security_Cheat_Sheet.html)
- [Drone Security Cheat Sheet](Drone_Security_Cheat_Sheet.html)

## 16. General Reference

- [Security Terminology Cheat Sheet](Security_Terminology_Cheat_Sheet.html)
