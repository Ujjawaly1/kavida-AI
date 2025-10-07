# Kavida-AI

**Automated Shipping Report Processor for Purchase Orders**

Kavida-AI is a Python-based tool that leverages Large Language Models (LLMs) to automatically extract, transform, and reconcile shipping report data from suppliers with existing Purchase Order (PO) information. It reduces manual effort, minimizes errors, and generates structured JSON outputs that can be easily integrated into inventory and ERP systems.

---

## Features

- Extracts shipping data from Excel reports  
- Matches shipments with existing POs and line items  
- Applies business rules for partial shipments and multiple releases  
- Produces structured JSON showing shipped, partially shipped, and on-order items  
- Flags unmatched shipments for review  
- Supports PO number normalization (prefix differences, numeric suffix matching)

---

## Installation

1. Clone the repository (if not already done):

```bash
git clone https://github.com/your-username/kavida-ai.git
cd kavida-ai
Create and activate a Python virtual environment:

bash
Copy code
python -m venv venv
source venv/bin/activate   # macOS/Linux
venv\Scripts\activate      # Windows
Install dependencies:

bash
Copy code
pip install -r requirements.txt
Set up OpenAI API key:

Create a .env file in the project root with:

bash
Copy code
OPENAI_API_KEY=your_api_key_here
Requirements
Python 3.9+

pandas

openai

python-dotenv

openpyxl (for Excel reading)

Usage
Place your supplier shipping report Excel file in the Given/ folder (e.g., Book.xlsx).

Update your current PO data in the process_shipping.py script or load dynamically from your system.

Run the processor:

bash
Copy code
python process_shipping.py
The output JSON (updated_po_data.json) will contain all current PO lines with shipping statuses:

json
Copy code
{
  "purchase_orders": [
    {
      "po_number": "H006374",
      "lines": [
        {
          "line_number": "33",
          "release_number": 1,
          "part_number": "182-837FE-TUFYH",
          "original_ordered_qty": 120,
          "shipped_qty": 15,
          "remaining_qty": 105,
          "shipment_date": "2025-04-23",
          "tracking_number": "k7822274320227929241",
          "status": "Partially Shipped"
        },
        {
          "line_number": "33",
          "release_number": 2,
          "part_number": "182-837FE-TUFYH",
          "original_ordered_qty": 120,
          "shipped_qty": 0,
          "remaining_qty": 105,
          "shipment_date": null,
          "tracking_number": null,
          "status": "On Order"
        }
      ]
    }
  ]
}
How It Works
Excel Reading: Pandas reads the supplier’s shipping report and converts it to a string table for the LLM.

PO Data Formatting: Existing POs are converted into a textual table for prompt inclusion.

LLM Processing: The prompt (GPT-4/GPT-5) instructs the model to:

Match shipments with POs

Apply business rules (partial shipment → new lines, multiple releases)

Normalize dates, quantities, and PO numbers

Produce structured JSON output

JSON Output: Parsed by Python and saved as updated_po_data.json.

Unmatched Rows: Any shipments that cannot be matched are flagged for manual review.

Assumptions About the Data
Shipping report Excel contains at least: PO NUMBER, PART NUMBER, PO LINE ITEM, QTY, DATE, TRACKING NUMBER.

Multiple shipments for a single line are represented as separate rows.

PO numbers may have different prefixes; matching is done using numeric suffix.

Dates are converted to ISO format YYYY-MM-DD.

Tracking numbers are unique per shipment.

Example Input / Output
PO JSON Input Sample:

json
Copy code
{
  "po_number": "Q781044",
  "line_items": [
    {
      "line_number": "1",
      "release_number": "7",
      "part_number": "JF37891",
      "order_qty": 35,
      "unit_price": 20.89,
      "due_date": "2025-03-24"
    }
  ]
}
Generated Output Sample:

json
Copy code
{
  "purchase_orders": [
    {
      "po_number": "Q781044",
      "lines": [
        {
          "line_number": "1",
          "release_number": 1,
          "part_number": "JF37891",
          "original_ordered_qty": 35,
          "shipped_qty": 35,
          "remaining_qty": 0,
          "shipment_date": "2025-03-24",
          "tracking_number": "k1234567890",
          "status": "Shipped"
        }
      ]
    }
  ]
}
License
MIT License – free to use and modify.
