# MedAxis² - Medical Pain Assessment System

## Overview
MedAxis² is a GDPR-compliant, multilingual (IT/EN/ES) professional platform for multi-dimensional pain assessment, primarily for dental and medical professionals focusing on temporomandibular disorders. It facilitates patient questionnaire distribution, response collection, and the generation of encrypted PDF reports for clinical documentation. The system operates on a subscription model (GO and PRO tiers) with integrated PayPal payments, aiming to enhance clinical practice and patient management through a robust, secure, and user-friendly tool. A Nociplastic Pain Module is available for PRO users. The application is configured as a Progressive Web App (PWA).

## User Preferences
Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend
The frontend is built with React 18+, TypeScript, and Vite, using Shadcn/ui (Radix UI, Tailwind CSS) for a Material Design aesthetic with HSL-based theming and light/dark modes. Wouter handles routing, and TanStack Query manages server state. Key features include a multi-step questionnaire, a customizable report viewer, an operator dashboard with patient management, and analytics with dynamic baseline selection. Comprehensive multi-language support is implemented using react-i18next. A custom password input component is used. The PWA setup includes a manifest, Service Worker for offline support, and relative path resolution for subdirectory deployment.

### Backend
The backend uses Express.js and TypeScript, providing a RESTful API. Authentication is handled via `passport-local` with sessions stored in PostgreSQL. It manages authentication (email-based), contact forms, questionnaire distribution, patient submissions, report generation, and PayPal processing. Features include email/WhatsApp integration, GDPR-compliant patient data handling, and 24-hour auto-expiration for questionnaire links. Admin-only features include an operator broadcast email system, a trial invite system, an app install & WhatsApp distribution system, and a demo patient system for new users. A persistent disclaimer system for MDR compliance is also integrated.

### Database
The system uses Drizzle ORM with Neon Serverless PostgreSQL. It includes tables for `users`, `operators`, `patients`, `questionnaireLinks`, `questionnaireResponses` (JSONB for core and nociplastic data), `contactRequests`, `sessions`, and `pendingSubscriptions`.

### PDF Generation
PDFKit generates AES-256-CBC encrypted PDF reports server-side, secured with patient-specific passwords. Reports include clinical information, demographics, multi-dimensional scores, total score, severity level, and interpretive descriptions. PRO users benefit from customization, no watermark, and unrestricted patient downloads, plus customizable clinical descriptions and integrated nociplastic scores.

### Subscription & Payment
Integrated with PayPal Server SDK for managing GO (free) and PRO (paid) subscriptions with automatic recurring billing. PayPal webhooks (BILLING.SUBSCRIPTION.*, PAYMENT.SALE.COMPLETED) are used for automatic subscription activation, renewal, cancellation, suspension, and expiration management.

### Security and Authentication
Email-only authentication is used, with `crypto.scrypt` for password hashing. Email verification with secure, expiring tokens is mandatory. GDPR compliance is ensured through data anonymization and multi-layered deletion strategies.

## External Dependencies
-   **PayPal Server SDK**: Payments and subscriptions.
-   **Neon Serverless PostgreSQL**: Database.
-   **Twilio**: WhatsApp notifications.
-   **SMTP Provider**: Email notifications.
-   **Radix UI**: Headless UI primitives.
-   **Shadcn/ui**: Pre-styled components.
-   **Vite**: Frontend build tool.
-   **TypeScript**: Full-stack type safety.
-   **Tailwind CSS**: CSS framework.
-   **Drizzle ORM**: Database ORM.
-   **TanStack Query**: Server state management.
-   **PDFKit**: Server-side PDF generation.
-   **passport-local**: Local authentication.
-   **connect-pg-simple**: PostgreSQL session storage.