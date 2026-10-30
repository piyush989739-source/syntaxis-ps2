# ReconcileIQ

> **Intelligent Financial Reconciliation & Audit System**

ReconcileIQ is an automated invoice-to-payment matching platform designed to resolve complex B2B accounting reconciliation challenges. It features automated duplicate detection, partial-payment combinatorial aggregation ("Split Match"), live metrics, and a tamper-evident audit trail with full undo capabilities.

![ReconcileIQ Banner](https://img.shields.io/badge/Status-Production%20Ready-emerald?style=for-the-badge)
![TypeScript](https://img.shields.io/badge/TypeScript-5.6-blue?style=for-the-badge&logo=typescript)
![React](https://img.shields.io/badge/React-18-cyan?style=for-the-badge&logo=react)
![Vitest](https://img.shields.io/badge/Vitest-2.1-success?style=for-the-badge&logo=vitest)

---

## Key Features

### 1. 🔍 Duplicate Payment Detection
- Automatically identifies likely duplicate payments based on:
  - Matching payer name / customer ID
  - Identical amount (calculated in integer paise)
  - Payment timestamps within 1 calendar day
  - High description similarity ($\ge 70\%$)
- Links the duplicate to the original transaction (`duplicateOf`) and excludes it from suggestions by default.
- Provides immediate actions: **Mark as Duplicate** (excluded from financial balances and recorded in the audit trail) or **Not a Duplicate** (restores to normal matching).

### 2. 🧩 Combinatorial Partial-Payment Aggregation ("Split Match")
- Finds combinations of 2 to 3 unallocated payments from the same customer whose amounts sum exactly to an unpaid invoice outstanding balance.
- Surfaces suggestions with transparent mathematical explanations:
  `PAY-2009 (₹12,000) + PAY-2010 (₹8,000) = ₹20,000 = INV-1010`
- Confirming applies allocations across each payment atomically.
- Includes a **Multi-Invoice Manual Split Modal** allowing a single payment to be allocated across several invoices with real-time balance validation.

### 3. 📜 Tamper-Evident Audit Trail & Rollback
- Logs all user actions (`confirm`, `reject`, `manual_assign`, `undo`, `mark_duplicate`, `unmark_duplicate`).
- Interactive **Undo** functionality reverses allocations, restores balances, and re-opens suggestions.
- Export full audit logs and reconciliation summary to CSV with one click.

### 4. 📊 Live Precision & Metrics
- Live-calculated KPIs: Precision, Correct-Suggestion Rate, and Unresolved Count.
- Interactive Recharts bar visualization showing confidence bands (High, Medium, Low) and flagged counts.
- **Confusion Matrix Log**: Tracks rejected or manually reassigned suggestions for accounting error discussions.

### 5. 🧮 Zero Float-Drift Financial Math
- Operates entirely in integer paise ($1\text{ Rupee} = 100\text{ paise}$) to avoid IEEE-754 precision drift (e.g. $0.1 + 0.2$ bugs).

---

## Getting Started

### Prerequisites
- Node.js 18+ (tested on Node v20.18.0)
- npm or pnpm

### Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/reconcileiq.git
cd reconcileiq

# Install dependencies
npm install

# Start development server
npm run dev
```

The application will be running at `http://localhost:5173/`.

---

## Running Unit Tests

ReconcileIQ includes a full suite of pure logic tests in `/src/lib/__tests__`:

```bash
npm test
```

### Test Coverage Highlights
- **Money**: Integer paise conversion and formatting without floating-point errors.
- **Parser/Validator**: Detection of missing amounts, invalid dates, negative amounts, and duplicate transaction IDs.
- **Reference Normalization**: Canonical resolution for formats like `"INV1003"`, `"inv-1004"`, and `"INV 1005"`.
- **Matcher**: Confidence scoring, ambiguous candidate detection, and amount mismatch tolerances.
- **Allocator**: Balance bounds enforcement, over-payment excess handling, and undo state restoration.
- **User Decisions**: Persistence of confirmed and rejected decisions across matching runs.

---

## Project Structure

```
reconcileiq/
├── src/
│   ├── components/       # UI components (KPICards, MatchDetailsPanel, Badges, Modals)
│   ├── context/          # Central reconciliation state & audit handlers
│   ├── lib/              # Pure logic modules
│   │   ├── __tests__/    # Vitest unit test suites
│   │   ├── aggregation.ts# Combinatorial split payment solver
│   │   ├── allocator.ts  # Double-entry ledger & allocation engine
│   │   ├── duplicates.ts # Duplicate payment heuristics
│   │   ├── matcher.ts    # Multi-factor confidence scoring
│   │   ├── money.ts      # Integer paise precision math
│   │   ├── normalizer.ts # Text & reference canonicalizers
│   │   ├── parser.ts     # CSV parser & validator
│   │   ├── sampleData.ts # Preloaded sample financial dataset
│   │   └── types.ts      # TypeScript interfaces
│   ├── screens/          # Primary views (Suggestions, Exceptions, Balances, Reports, Import)
│   ├── App.tsx           # Layout & navigation root
│   └── main.tsx          # Application entrypoint
├── package.json
├── tailwind.config.js
└── vite.config.ts
```

---

## License
MIT
