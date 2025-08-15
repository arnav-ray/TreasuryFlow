**TreasuryFlow - ERP Agnostic Treasury Management Platform**
1. Introduction
TreasuryFlow is a comprehensive, fully interactive front-end prototype for an enterprise-grade treasury and cash management platform. It is designed to provide global finance teams with a single source of truth by seamlessly integrating with any backend ERP system, eliminating data silos, and empowering strategic financial decision-making.
This project showcases a sophisticated, data-driven user interface built with modern web technologies and demonstrates a clear vision for an AI-augmented future for financial operations.

2. Key Features
CFO-Ready Dashboard: A high-level overview of the company's global financial position, including multi-currency cash positions, AP/AR outstanding totals, and predictive cash flow forecasts. Features multiple interactive charts for AP status and AR aging.
Granular AP & AR Management: Detailed views of payables and receivables, with interactive modals to drill down into individual invoice details, associated documents (POs, GRs), and approval histories.
Advanced Treasury Operations: A dedicated module for treasury professionals, featuring:
A live (delayed) market data ticker.
Consolidated multi-currency cash positions across various international banks.
A proof-of-concept module for modeling and managing FX hedging derivatives (Forwards & Options).
Interactive Process Flows: Visual, end-to-end representation of core financial processes like Procure-to-Pay (P2P) and Order-to-Cash (O2C). Selecting a document instantly highlights its current stage in the workflow.
Enterprise Governance & Security: A dedicated module demonstrating concepts for:
Role-Based Access Control (RBAC) with a detailed permissions matrix.
A live API connection status monitor.
An immutable, real-time audit log.

Gemini API Integration:
Live AI Treasury Assistant: A dashboard chatbot that uses the Gemini API to provide intelligent, context-aware answers to financial questions based on live KPI data.
AI-Powered Email Drafting: An assistive feature in the AP module that leverages the Gemini API to automatically draft professional follow-up emails for invoices.

3. Technology Stack
Front-End: HTML5, JavaScript (ES6+)
Styling: Tailwind CSS for a utility-first design system.
Charting: Chart.js for interactive and responsive financial charts.
Icons: Font Awesome
AI & LLM: Google Gemini API for the intelligent assistant and email drafting features.
Live Market Data: TradingView Widgets for the delayed market data ticker.

4. How to Run
This prototype is built as a single, self-contained index.html file with no external dependencies beyond the CDN-hosted libraries.
Clone the repository or download the index.html file.
Open the index.html file in any modern web browser (Chrome, Firefox, Edge, Safari).
The application will run entirely client-side.

5. Demo Accounts
Use the following credentials to log in and explore the different user roles:
Username: admin | Password: password (CFO role with full permissions)
Username: finance | Password: password (Controller role with limited permissions)
Username: approver | Password: password (Manager role with approval-only permissions)
