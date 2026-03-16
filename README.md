# Quote Request Execution Chain Demo for Oracle APEX

This repository contains a simple Oracle APEX demo application that illustrates a very common page processing pattern: **one process producing outputs that become inputs for the next process**.

The goal of this demo is to show how an APEX page can behave like a small **execution chain**, and how different types of intermediate state can be handled in different ways depending on their nature.

## What this demo shows

This application demonstrates a four-step processing flow triggered by a single button click:

1. **Validate Request**  
   Validates the user input and normalizes key values.

2. **Invoke Pricing API**  
   Simulates a pricing call through a PL/SQL package and returns both scalar outputs and a JSON payload.

3. **Build Quote Lines Collection**  
   Expands the structured response into an **APEX collection** for temporary row-based handling.

4. **Save Quote**  
   Persists the final quote header and line items into database tables.

This progression is intentional:

- **scalar outputs** are passed through **hidden page items**
- **structured temporary data** is stored in an **APEX collection**
- **final business data** is persisted in **database tables**

## Why this demo exists

In many real Oracle APEX applications, the challenge is not just running a process from a button click. The real challenge is deciding **how the output of one step should become the input of the next**.

This demo was built to support a content piece around that idea and to show a practical way of organizing page processing when:

- one process depends on values produced earlier
- hidden items are useful, but not always enough
- temporary structured data needs a better handoff strategy
- the final result must be persisted as business data

## Files

- **`f120.sql`** — Oracle APEX application export

## Application flow

The demo page uses a simple quote generation scenario.

The user provides:

- Customer Document
- Product
- Quantity
- ZIP Code
- Sales Channel

After clicking **Generate Quote**, the page executes the following chain:

### Step 1 — Validate Request
Inputs:
- Customer document
- Product ID
- Quantity
- ZIP Code

Outputs:
- Customer ID
- Normalized ZIP
- Trace ID
- Validation status/message

### Step 2 — Invoke Pricing API
Inputs:
- Customer ID
- Product ID
- Quantity
- Normalized ZIP
- Trace ID

Outputs:
- Quote ID
- Total Amount
- Currency
- Status Code
- Response JSON

### Step 3 — Build Quote Lines Collection
Input:
- Response JSON

Output:
- Temporary quote lines stored in an APEX collection

### Step 4 — Save Quote
Inputs:
- Scalar values from page items
- Quote lines from the collection

Outputs:
- Persisted quote header
- Persisted quote lines
- Internal quote number

## Database objects used

The application relies on a small demo schema footprint:

- `DEMO_QUOTE_HDR`
- `DEMO_QUOTE_LINE`
- `PKG_DEMO_QUOTE`

The package implements the four logical steps of the execution chain:

- `VALIDATE_REQUEST`
- `INVOKE_PRICING`
- `RESPONSE_TO_COLLECTION`
- `SAVE_QUOTE`

## Prerequisites

Before importing the application, make sure the required database objects have already been created in the parsing schema.

This demo also expects **Oracle APEX** to be available, since it uses:

- page processes
- hidden page items
- session state
- `APEX_COLLECTION`

## Import instructions

1. Open **Oracle APEX App Builder**
2. Import the application from the file:
   - `f120.sql`
3. Make sure the parsing schema contains the required database objects
4. Run the application
5. Open the demo page and execute the quote flow

## Suggested test data

Use the following values for a predictable demo run:

- **Customer Doc:** `123.456.789-00`
- **Product:** `PRD-100`
- **Qty:** `1`
- **ZIP Code:** `89010-025`
- **Sales Channel:** `WEB`

## What to look for in the demo

When running the application, pay attention to how each stage handles state differently:

- validation outputs are kept as **page-level scalar state**
- API response details are partly kept in **hidden items**
- quote lines are transformed into **temporary row-based data**
- final records are stored in **database tables**

That separation is the main point of the demo.

## Main idea

A button click may look simple in the UI, but behind it there can be a small execution chain.

This demo is meant to show that:

- not every process output should be treated the same way
- not every output belongs in hidden items
- the best handoff strategy depends on whether the state is:
  - scalar or structured
  - temporary or persistent
  - page-specific or broader than the current step

## Related content

This demo supports a broader discussion around **page processing design in Oracle APEX**, especially when one process feeds the next and state must move cleanly across the chain.

## Notes

This is a demo app intended for learning and illustration purposes.  
The pricing step is simulated locally through PL/SQL so the flow stays deterministic and easy to reproduce.

## License

You may adapt this demo for learning, experimentation, and internal proof-of-concept usage.
