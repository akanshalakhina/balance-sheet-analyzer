# 📊 Balance Sheet Analyzer

A full-stack web application that lets users **upload a company's balance sheet** (PDF or Excel), automatically **extracts the financial data**, computes **key financial ratios**, and generates **actionable insights** — all processed locally with no external API dependency.

---

## 🎯 What It Does

1. **Upload** a balance sheet document (PDF or Excel)
2. **Extract** structured financial data — Assets, Liabilities, Equity, and all sub-items
3. **Calculate** financial ratios — Current Ratio, Debt-to-Equity, Working Capital, and more
4. **Generate** insights — strengths, concerns, year-over-year trends
5. **Display** everything in a clean, interactive dashboard

> Works with **scanned/OCR'd PDFs** too — handles damaged text, broken numbers, and skewed pages.

---

## 🖥️ Screenshots

After uploading a balance sheet, the app shows:

- **Company Info** — name, CIN, reporting periods, currency
- **Accounting Identity Check** — verifies Assets = Equity + Liabilities
- **Extracted Balance Sheet** — structured table with all line items
- **Financial Ratios** — computed metrics with formulas
- **Composition Charts** — visual breakdown of assets and liabilities
- **Insights** — automated findings sorted by severity

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND (React)                     │
│  Upload Panel → Report Header → Statement Table          │
│  Metrics Grid → Composition Charts → Insights Panel      │
└──────────────────────┬──────────────────────────────────┘
                       │  POST /api/analyze (multipart)
                       ▼
┌─────────────────────────────────────────────────────────┐
│                     BACKEND (Express)                     │
│                                                           │
│  ┌─────────┐   ┌─────────┐   ┌───────────┐              │
│  │ INGEST  │──▶│  PARSE  │──▶│  ANALYZE  │              │
│  │         │   │         │   │           │              │
│  │ PDF     │   │ Numbers │   │ Derive    │              │
│  │ Excel   │   │ Rows    │   │ Ratios    │              │
│  │         │   │ Align   │   │ Insights  │              │
│  │         │   │ Classify│   │           │              │
│  └─────────┘   └─────────┘   └───────────┘              │
│                                                           │
│  100% Deterministic & Local Analysis (No External APIs)   │
└─────────────────────────────────────────────────────────┘
```

### Data Flow

```
PDF/Excel  ──▶  Raw Text with Positions  ──▶  Structured Numbers
           ──▶  Balance Sheet Table      ──▶  Financial Ratios
           ──▶  Insights & Analysis      ──▶  JSON Response
```

---

## 🛠️ Tech Stack

| Layer         | Technology               | Purpose                                      |
|---------------|--------------------------|----------------------------------------------|
| **Frontend**  | React 18 + TypeScript    | User interface                               |
| **Bundler**   | Vite                     | Fast dev server and build tool               |
| **Styling**   | Tailwind CSS             | Responsive, utility-first styling            |
| **Charts**    | Recharts                 | Composition & ratio visualizations           |
| **Icons**     | lucide-react             | UI icons                                     |
| **Backend**   | Node.js 20 + Express 4   | REST API server                              |
| **Language**   | TypeScript               | Type safety across the full stack             |
| **PDF Parse** | pdfjs-dist               | Extracts text with x/y coordinates from PDFs |
| **Excel Parse**| xlsx (SheetJS)          | Reads .xlsx, .xls, .xlsm, .csv files        |
| **Upload**    | multer                   | Handles multipart file uploads (in memory)   |
| **Validation**| zod                      | Schema validation for data integrity         |
### Third-Party APIs / External Services

**None.** All processing happens locally on the server. There are no external API calls, third-party subscriptions, or API keys required. No `.env` setup is needed to run the app.

---

## 🚀 Getting Started

### Prerequisites

- **Node.js 20.11** or higher ([download](https://nodejs.org/))

### Installation & Run

```bash
# 1. Clone the repository
git clone <repository-url>
cd balance-sheet-analyzer

# 2. Install dependencies
npm install

# 3. Start the app (frontend + backend together)
npm run dev
```

Then open **http://localhost:5173** in your browser.

- Frontend runs on `http://localhost:5173`
- Backend API runs on `http://localhost:5174`
- Vite proxies `/api` requests to the backend automatically


### Other Commands

```bash
npm test           # Run 90 automated tests
npm run build      # Build for production
npm run typecheck  # Type-check both frontend and backend
```

---

## 📁 Project Structure

```
balance-sheet-analyzer/
├── api/                          # Vercel serverless entry point
│   └── [...path].ts              # Catch-all route for /api/*
│
├── server/                       # Backend (Node + Express)
│   └── src/
│       ├── index.ts              # Server entry point
│       ├── app.ts                # Express app configuration
│       ├── config.ts             # Environment & config settings
│       ├── http/                 # HTTP layer
│       │   ├── routes.ts         # API route definitions
│       │   ├── upload.ts         # File upload handling (multer)
│       │   └── errors.ts         # Error handling middleware
│       ├── ingest/               # File readers
│       │   ├── pdf.ts            # PDF text extraction (pdfjs-dist)
│       │   ├── excel.ts          # Excel/CSV reader (SheetJS)
│       │   └── types.ts          # Shared token model
│       ├── parse/                # Data extraction engine
│       │   ├── numbers.ts        # Number parsing & OCR repair
│       │   ├── rows.ts           # Table row reconstruction
│       │   ├── align.ts          # Caption-to-figure alignment
│       │   ├── classify.ts       # Fuzzy caption matching
│       │   ├── taxonomy.ts       # Chart of accounts
│       │   ├── periods.ts        # Year & currency detection
│       │   └── balanceSheet.ts   # Final structure assembly
│       ├── analyze/              # Financial analysis
│       │   ├── derive.ts         # Total computation
│       │   ├── ratios.ts         # Financial ratio calculations
│       │   └── insights.ts       # Rule-based insight engine
│       ├── domain/
│       │   └── types.ts          # API contract types
│       └── __tests__/            # Test suite (90 tests)
│
├── web/                          # Frontend (React + Vite)
│   └── src/
│       ├── main.tsx              # React entry point
│       ├── App.tsx               # Main application component
│       ├── types.ts              # TypeScript type definitions
│       ├── index.css             # Global styles
│       ├── lib/                  # API client & utilities
│       └── components/
│           ├── UploadPanel.tsx    # File upload interface
│           ├── ReportHeader.tsx   # Company info & confidence
│           ├── StatementTable.tsx # Extracted balance sheet table
│           ├── MetricsGrid.tsx    # Financial ratios display
│           ├── CompositionCharts.tsx # Visual charts
│           ├── InsightsPanel.tsx  # Analysis & findings
│           └── States.tsx        # Loading, error, empty states
│
├── samples/                      # Sample balance sheet for testing
│   └── laj-exports-fy2024.pdf
│
├── .env.example                  # Environment variable template
├── package.json                  # Root package (npm workspaces)
├── vercel.json                   # Vercel deployment config
└── README.md                     # This file
```

---

## 🔍 Approach: How Financial Data Is Extracted

### The Challenge

Balance sheets in PDFs — especially scanned Indian filings — are not simple to read. The text layer can be damaged, tables have no structure, and pages may be skewed. Here's what the actual text looks like from a scan:

```
1,r7,68"77.414      5t,39,75,424       79,88,10,63 I
2t,35,t I,7 10      1.43.80.70.840     Shortterm borowings
```

### Step 1: Ingest — Read the Document

Both PDF and Excel are converted into the same neutral format: a list of positioned tokens `{ text, x, y, width, height }`. This means all downstream logic is shared regardless of input format.

- **PDF**: `pdfjs-dist` extracts text with exact x/y coordinates
- **Excel**: Grid positions are synthesized from row/column indices

### Step 2: Parse — Understand the Numbers

**Number parsing** handles:
- Indian digit grouping (`1,17,68,77,414`)
- OCR-damaged characters (`l`→1, `O`→0, `S`→5, `t`→1)
- Shattered tokens (`["79,88,10,63", "I"]` → one number)
- Decimal vs separator ambiguity (`1.43.80.70.840` is an integer, not a decimal)

**Table reconstruction** clusters tokens by their y-coordinate (baseline) to rebuild rows, and identifies columns by clustering the right edges of numeric tokens (since financial figures are right-aligned).

**Caption alignment** uses a dynamic programming algorithm (Needleman-Wunsch) to correctly pair each figure with its caption, even when the page is skewed and numbers sit below their actual label.

**Classification** matches extracted captions against a comprehensive chart of accounts (Schedule III, IFRS, US-GAAP) using fuzzy string matching that handles misspellings and OCR damage.

### Step 3: Analyze — Compute Metrics

**Totals are computed, not trusted.** Printed subtotals on scanned documents are often the most damaged, so every roll-up is derived from its components. Printed totals serve only as a cross-check.

**Financial ratios computed:**

| Ratio                        | Formula                                        |
|------------------------------|-------------------------------------------------|
| Current Ratio                | Current Assets ÷ Current Liabilities            |
| Quick Ratio                  | (Current Assets − Inventory) ÷ Current Liabilities |
| Cash Ratio                   | Cash & Equivalents ÷ Current Liabilities        |
| Working Capital              | Current Assets − Current Liabilities            |
| Total Debt                   | Short-term + Long-term Borrowings               |
| Net Worth                    | Total Equity (Share Capital + Reserves)          |
| Debt-to-Equity               | Total Debt ÷ Total Equity                       |
| Asset-to-Liability           | Total Assets ÷ Total Liabilities                |
| Debt-to-Assets               | Total Debt ÷ Total Assets                       |
| Equity Ratio                 | Total Equity ÷ Total Assets                     |
| Inventory Share              | Inventory ÷ Current Assets                      |

**Insights** are generated by a rule engine that examines the ratios and year-over-year changes, producing findings categorized as strengths, concerns, trends, or observations — sorted by severity.


---

## 🧪 Testing

```bash
npm test
```

**90 tests** across six test files:

| Test File          | What It Covers                                           |
|--------------------|---------------------------------------------------------|
| `numbers.test.ts`  | Indian/Western grouping, negatives, OCR repairs          |
| `align.test.ts`    | Skewed page alignment verification                       |
| `classify.test.ts` | Damaged caption matching, confusion traps                |
| `periods.test.ts`  | Column header years, currency unit detection             |
| `analysis.test.ts` | Roll-ups, balance check, ratios, insight rules           |
| `pipeline.test.ts` | End-to-end on real scanned PDF and Excel workbooks       |

All tests run offline — no API key needed.

---

## ⚠️ Error Handling

The app handles invalid inputs gracefully with specific error messages:

| Scenario                  | Response                                              |
|---------------------------|-------------------------------------------------------|
| Image-only PDF (no text)  | Advises user to run OCR first                         |
| Corrupt or encrypted PDF  | Clear error message                                   |
| Wrong file type           | Rejected on both client and server side               |
| File too large            | Shows the size limit                                  |
| Sparse data found         | Succeeds with warnings and lower confidence           |
| No readable line items    | Shows empty state instead of crashing                 |

---

