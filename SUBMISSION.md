# Submission

- **Name:** Karpaganathan M.
- **Date submitted:** 14 November 2026
- - **Repository link:** https://github.com/Karpaganathan-389/pse-support-lab
- **Total time spent (real time):** Approximately 6 hours

> Replies below are drafts written on the **exercise clock** (Monday 16 November 2026, Asia/Dubai). Keep your real time in the time log at the end. Put screenshots, log excerpts and query output in `evidence/` and link to them.

> **Required:** the Acknowledgement and Priority sections for **all five** items, and the full write-up (investigation log onwards) for **at least three** items of your choice. For an item you did not fully investigate, replace the remaining sections with "Not investigated — what I would check next: …".

- **Items I fully investigated, and why I chose them:**

I fully investigated Incidents 1, 3, 4 and 5.

- **Incident 1 – Oasis Builders:** Selected-company and seller-TRN mismatch caused all eight invoices to be rejected. This was important because it affected the customer's month-end invoicing.
- **Incident 3 – Al Noor Fuel:** Investigated BR-CO-15 rejection and compared invoice totals with the PINT payload to identify the AED 0.01 discrepancy.
- **Incident 4 – Bluepeak Sahara:** Investigated the batch of 40 rejected invoices and identified the seller-TRN mismatch that prevented ASP submission.
- **Incident 5 – Ghaf Tree Café:** Investigated date-format and seller-TRN validation errors affecting the customer's four-invoice batch.

Incident 2 was partially investigated. Available historical monitoring data did not establish the cause of the API restart alert, so I documented the evidence gaps and the next checks required.

## Incident 1 — `inbox/01-oasis-builders-upload-stuck.md`

### Acknowledgement

- **Draft prepared at:** 09:10 GST, 16 Nov 2026 (not sent to a real customer)
- **Reply:**

> Hi Rashid,
>
> Thank you for reporting this. I understand the urgency, especially with your month-end close.
>
> Please do not retry the upload or manually recreate the invoices for now. I am checking the upload attempts and invoice statuses to identify which invoices were processed and how to safely proceed without duplicates.
>
> I will share the verified status and next steps as soon as possible this morning.
>
> Regards,  
> Product Support Team

### Priority and justification

- **Priority:** P2
- **Why:** All eight invoices in the November upload attempts were rejected due to a seller TRN and selected-company mismatch. This affects the customer's month-end invoicing, but there is no evidence of a wider production outage.
- **SLA deadlines I am working to:**
  - First response: within 1 hour of receipt, by 10:05 GST on 16 Nov 2026.
  - Next update: every 2–4 hours, between 11:05 and 13:05 GST.
  - Resolution target: 8 business hours, by 17:05 GST on 16 Nov 2026.

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 09:10 GST | Reviewed the customer report and prepared acknowledgement | Customer reported a November upload stuck in Processing after three retries |
| Not recorded | Reviewed the uploaded workbook | 600 line rows representing 8 unique invoices; mandatory fields were populated |
| Not recorded | Reviewed portal import history | Four upload attempts recorded; each contained 600 rows and 8 invoices |
| Not recorded | Checked invoice rejection details | All 8 invoices were rejected with AE-BR-10 |
| Not recorded | Compared selected company with source seller details | Al Noor Fuel & Lubricants LLC was selected, but the workbook seller was Oasis Builders Supply LLC |
| Not recorded | Checked ASP references and submission status | No ASP references; invoices were not confirmed as submitted to the ASP |
| Not recorded | Compared with previous October batch | October Oasis batch showed 8 accepted invoices |

### Root cause

- **What happened:** The November upload was made with Al Noor Fuel & Lubricants LLC selected, while the uploaded workbook belonged to Oasis Builders Supply LLC. The seller TRN did not match the selected company.
- **Where:** Portal upload company selection and invoice validation.
- **Why it happens:** The portal validates the seller TRN in the uploaded invoice against the selected company. The mismatch triggered AE-BR-10.
- **Evidence:**
  - `evidence/incident-1-oasis-import-history.png`
  - `evidence/incident-1-obs-001-ae-br-10.png`
- **Confidence and open questions:** High confidence. The portal rejection explicitly confirms the TRN mismatch. The correct company/TRN configuration should be verified before any re-upload.

### Customer resolution reply OR escalation note

- **Which one and why:** Customer resolution reply. This is a portal configuration issue, and no engineering escalation is currently required.

> Hi Rashid,
>
> We identified why the November upload was rejected. The seller TRN in the uploaded file does not match the company selected during upload.
>
> All eight invoices in the four November upload attempts show the same portal validation error. No ASP submission reference or response is recorded for these invoices.
>
> Please verify the correct company selection and seller TRN before uploading again. Please do not retry until the invoice status and duplicate checks are complete.
>
> Regards,  
> Product Support Team

### Workaround offered

- Stop further retries of the same upload until the company selection is corrected.
- Confirm that Oasis Builders Supply LLC is configured correctly in the portal and select it before uploading.
- Verify that the seller TRN in the workbook matches the selected company's registered TRN.
- Check the status of all eight invoices and confirm there are no existing successful submissions before arranging a controlled re-upload.
- After re-upload, verify each invoice status and confirm ASP references are available.

### Prevention ideas

- Add a pre-upload validation comparing the seller TRN in the file with the selected company's TRN.
- Display a clear warning showing both the seller name in the file and the selected company name.
- Block submission when the seller TRN and selected company do not match.
- Provide a clear per-invoice result report after every upload attempt.
- Warn users before retrying a batch that has already been rejected, and explain the reason for rejection.

## Incident 2 — `inbox/02-alert-api-restarted-marina-bay.md`

### Acknowledgement

- **Draft prepared at:** 09:55 GST, 16 Nov 2026 (not sent to a real customer)
- **Sent to:** Priya Nair, CSM (forwarded incident report)
- **Reply:**

> Hi Priya,
>
> Thanks for forwarding the alert and customer feedback. I understand the concern about intermittent 502 errors and invoices remaining in Validated status, especially with Marina Bay's go-live approaching.
>
> I am checking the API health, restart history, error logs and invoice submission status to understand the impact.
>
> Could you confirm whether any production customers are currently unable to submit invoices to the ASP? If confirmed, I will treat this as a P1 and escalate to Engineering immediately.
>
> I will provide the next update by 13:50 GST.
>
> Regards,  
> Product Support Team

### Priority and justification

- **Priority:** P2 (provisional, pending impact confirmation)
- **Why:** The alert reports three API restarts within 15 minutes, and customers report intermittent 502 errors and invoices stuck in Validated status. The available evidence does not yet confirm a complete production outage or that no invoices are reaching the ASP. Upgrade to P1 and page Engineering immediately if that impact is confirmed.
- **SLA deadlines I am working to:**
  - First response: within 1 hour of Priya's report at 09:50 GST, by 10:50 GST on 16 Nov 2026.
  - Next update: every 2–4 hours, by 13:50 GST.
  - Resolution target: 8 business hours, by 17:50 GST on 16 Nov 2026.

### Investigation log

| Time | What I checked / did | What I found |
|---|---|---|
| Not recorded | Queried Prometheus using `changes(process_start_time_seconds{job="api"}[6h])` | Result was 1 at the available evaluation time of 14 Nov 2026 05:51:55. This is historical data and does not confirm a restart during the 16 Nov alert window. |

### Root cause

- **What happened:** The alert reported three API restarts within 15 minutes, alongside intermittent 502 errors and invoices stuck in Validated status. However, the available monitoring data does not establish what caused the reported restarts.
- **Where (component, `file:line`):** Invoicely API service (`invoicely-lite-api-1`). Exact failure location is not confirmed.
- **Why it happens:** Unknown. Possible areas to investigate include database connectivity, application process exits, container resource limits and unexpected server errors. These are hypotheses, not confirmed causes.
- **Evidence:**
  - Grafana Loki query for 16 Nov 2026, 09:30–10:30 GST returned no logs.
  - Prometheus query `changes(process_start_time_seconds{job="api"}[6h])` returned `1` at the available historical evaluation time of 14 Nov 2026 05:51:55. This does not prove a restart on 16 Nov.
  - Evidence screenshot: `evidence/incident-2-prometheus-restart-query.png`.
- **Confidence and open questions:** Low confidence in the underlying root cause. Historical logs and metrics for the actual alert window are unavailable. Need to retrieve production logs, container events, resource metrics, affected invoice IDs and ASP submission records to establish impact and cause.

### Customer resolution reply OR escalation note

- **Which one and why:** Customer/CSM update draft. The available evidence is insufficient to confirm the root cause or full production impact. A confirmed P1 outage would require immediate Engineering escalation.

> Hi Priya,
>
> Our initial checks confirm that the API restart alert and customer reports need further investigation. However, the available local logs do not cover the 16 November alert window, so we cannot yet confirm the restart cause.
>
> Please share the affected invoice IDs and confirm whether any production invoices are currently unable to reach the ASP.
>
> We are treating this as a provisional P2 while validating the impact. If production invoice submission is confirmed down, we will upgrade it to P1 and page Engineering immediately.
>
> Please avoid retrying affected invoices until their submission status and duplicate risk are verified. I will provide the next update by 13:50 GST.
>
> Regards,  
> Product Support Team

### Workaround offered

- Identify the affected invoice IDs and check their current portal and ASP submission statuses.
- Do not retry invoices while their submission status or duplicate risk is uncertain.
- If the API is still unstable, coordinate with Engineering for a safe recovery plan.
- Once service stability is confirmed, verify each affected invoice before arranging any controlled retry.
- Keep Priya and the customer updated every 2–4 hours while the incident remains P2. If P1 impact is confirmed, follow the P1 update cadence.

### Prevention ideas

- Retain API logs and container events long enough to investigate historical incidents.
- Add alerts for repeated API restarts and sustained HTTP 5xx errors.
- Monitor invoices stuck in Validated status beyond an expected processing threshold.
- Track ASP submission success and failure rates.
- Include affected invoice IDs and correlation IDs in operational logs.
- Document a safe recovery procedure that checks invoice status before retrying submissions.


## Incident 3 — `inbox/03-al-noor-fuel-br-co-15.md`

### Acknowledgement

- **Draft prepared at:** 10:20 GST, 16 Nov 2026 (not sent to a real customer)
- **Reply:**

> Hi Fatima,
>
> Thank you for sharing the details. I understand the importance of resolving these rejections, particularly while your external auditor is reviewing the November VAT position.
>
> We are investigating the rejected invoices and comparing their VAT totals with the accepted invoices in the same upload.
>
> Please avoid manually changing or re-uploading the rejected invoices until we identify the exact discrepancy. I will share our findings and recommended correction steps by 12:15 GST.
>
> Regards,  
> Product Support Team

### Priority and justification

- **Priority:** P2
- **Why:** Three out of six invoices in the November batch were rejected by the ASP with BR-CO-15. This affects a subset of invoices and creates a significant finance and audit impact, but there is no evidence of a complete production outage.
- **SLA deadlines I am working to:**
  - First response: within 1 hour of receipt (by 11:15 GST, 16 Nov 2026).
  - Resolution target: 8 business hours. The target extends to 08:15 GST on 17 Nov because the business-hours window ends at 18:00 GST on 16 Nov.
  - Updates: every 2–4 hours; next update due between 12:15 and 14:15 GST.

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 10:20 GST | Reviewed the customer report and acknowledgement requirements | Customer reported BR-CO-15 rejections affecting November invoices |
| Not recorded | Checked the November import in the portal | Six invoices and 13 rows; three accepted and three rejected |
| Not recorded | Opened invoice ANF-2611-0042 and reviewed its ASP response | Rejected by ASP with BR-CO-15; actual total AED 15,022.42, expected AED 15,022.43 |
| Not recorded | Reviewed the invoice status history | Parsed, Validated and Submitted, then Rejected by ASP; no successful acceptance for this invoice |
| Not recorded | Reviewed the PINT payload for invoice 0042 | Tax-exclusive amount AED 14,307.08 + VAT AED 715.35 = expected AED 15,022.43, but tax-inclusive and payable amounts are AED 15,022.42 |
| Not recorded | Inspected `services/api/internal/pint/pint.go` | The totals structure assigns `TaxInclusiveAmount` and `PayableAmount` from the rounded `gross` value. The exact source of the one-fils discrepancy still needs code-level tracing |

### Root cause

- **What happened:** Invoice ANF-2611-0042 was rejected because its invoice-level gross total does not equal the tax-exclusive amount plus the VAT amount. The payload contains AED 14,307.08 tax-exclusive and AED 715.35 VAT, which should produce AED 15,022.43. However, both `taxInclusiveAmount` and `payableAmount` were sent as AED 15,022.42.
- **Where:** PINT payload generation, `services/api/internal/pint/pint.go` (exact line number not yet verified).
- **Why it happens:** The generated payload uses the rounded `gross` value for both invoice-level totals. The evidence confirms that the generated totals are inconsistent by AED 0.01. The precise calculation or rounding step that introduces the discrepancy has not yet been confirmed.
- **Evidence:**
  1. `evidence/incident-3-invoice-0042-rejection.png` — portal rejection and BR-CO-15 details.
  2. `evidence/incident-3-pint-totals-code.png` — PINT totals-generation code.
  3. ASP response message ID: `8ed3e78b-5f72-4fd3-9e42-f3f246fc100f`.

- **Confidence and open questions:** High confidence in the payload-level total mismatch for invoice 0042. The exact code-level rounding cause is not yet confirmed. The same payload comparison should be completed for rejected invoices 0044 and 0046 before concluding that all three share the same cause.

### Customer resolution reply OR escalation note

- **Which one and why:** Customer resolution reply draft. The available evidence identifies a specific payload inconsistency for invoice 0042. The code-level cause and the other rejected invoices still need verification, so avoid claiming a complete fix.

> Hi Fatima,
>
> We reviewed invoice ANF-2611-0042 and confirmed that the ASP rejected it with BR-CO-15 because the invoice-level total is AED 0.01 lower than the expected amount.
>
> The payload contains a tax-exclusive amount of AED 14,307.08 and VAT of AED 715.35. These add up to AED 15,022.43, while the submitted tax-inclusive and payable amounts are AED 15,022.42.
>
> We are checking the remaining rejected invoices and tracing the total calculation before recommending a correction. Please hold further uploads of the rejected invoices until we confirm the safe correction and re-submission process.
>
> We will provide the next update by 14:15 GST.
>
> Regards,  
> Product Support Team

### Workaround offered

- Do not repeatedly upload or manually alter the rejected invoices while their status and totals are being checked.
- Keep the three accepted invoices unchanged.
- Verify the corrected tax-inclusive and payable totals for each rejected invoice before arranging a controlled re-submission.
- Confirm the final invoice status and ASP response after any approved correction.

### Prevention ideas

- Calculate invoice-level tax-inclusive totals consistently from the rounded tax-exclusive amount and VAT amount.
- Add a pre-submission validation that checks `taxInclusiveAmount = taxExclusiveAmount + taxAmount`.
- Add unit tests for rounding boundaries and multi-line invoices.
- Log the calculated net, VAT and gross totals for troubleshooting.
- Show a clear validation error before submission when invoice totals do not reconcile.

---
## Incident 4 — `inbox/04-bluepeak-sahara-not-received.md`

### Acknowledgement

- **Draft prepared at:** 11:35 GST, 16 Nov 2026 (not sent to a real customer)
- **Reply:**

> Hi Daniel,
>
> Thank you for reporting this. I understand the concern about invoices not reaching Emirates Palm Hospital.
>
> Please hold off on resending the batch. Our initial checks show that the invoices were rejected during portal validation because the seller TRN in the uploaded file does not match the selected company.
>
> We are checking the batch and individual invoice records to confirm the scope and safe next steps. I will share a verified update by 15:30 GST.
>
> Regards,  
> Product Support Team

### Priority and justification

- **Priority:** P2
- **Why:** All 40 invoices in the batch show portal rejection. This is a significant batch-level issue affecting the customer's payment workflow. However, there is no evidence of a system-wide outage.
- **SLA deadlines I am working to:**
  - First response: within 1 hour of receipt, by 12:30 GST on 16 Nov 2026.
  - Resolution target: 8 business hours, by 09:30 GST on 17 Nov 2026.
  - Updates: every 2–4 hours; next update due between 13:30 and 15:30 GST.

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 11:35 GST | Reviewed the customer report | Connector received HTTP 201; portal showed Submitted; buyer reported missing invoices |
| Not recorded | Opened the Sahara batch in the portal | Batch contains 40 invoices and 60 source rows; all 40 are rejected by portal validation |
| Not recorded | Reviewed batch company details | Selected company is Al Noor Fuel & Lubricants LLC, but source file seller is Sahara Medical Supplies FZE |
| Not recorded | Inspected invoice SMS-1116-001 | Error AE-BR-10: seller TRN does not match selected company; no ASP reference or response |
| Not recorded | Inspected invoice SMS-1116-008 | Same AE-BR-10 error; no ASP reference or response |
| Not recorded | Compared batch and customer email timestamps | Portal batch shows upload on 14 Nov 2026, while customer report is dated 16 Nov 2026. This discrepancy needs clarification |

### Root cause

- **What happened:** The Sahara Medical batch was uploaded with Al Noor Fuel & Lubricants LLC selected as the company, even though the file identifies Sahara Medical Supplies FZE as the seller. The seller TRN therefore failed portal validation.
- **Where:** Portal upload/company selection and seller TRN validation. Exact source code location not yet verified.
- **Why it happens:** The selected company's TRN does not match the seller TRN in the uploaded file. The portal rejected the invoices before ASP submission.
- **Evidence:**
  1. `evidence/incident-4-sms-001-ae-br-10.png` — invoice 001 rejection.
  2. `evidence/incident-4-sms-008-ae-br-10.png` — invoice 008 rejection.
  3. Batch details: 40 invoices rejected by Portal; ASP references empty.
  4. Source file identifies Sahara Medical Supplies FZE and seller TRN `100645371900003`.
- **Confidence and open questions:** High confidence that the selected-company and seller-TRN mismatch caused the two inspected invoice rejections. The batch summary reports 40/40 portal rejections. Confirming the same error across all 40 individually and clarifying the 14 Nov versus 16 Nov timestamp discrepancy remain open.

### Customer resolution reply OR escalation note

- **Which one and why:** Customer resolution reply draft. The portal evidence identifies a company-selection/TRN mismatch. No engineering escalation is currently justified by the evidence.

> Hi Daniel,
>
> We identified why the Sahara batch did not reach the buyer. The uploaded file identifies Sahara Medical Supplies FZE as the seller, but the portal batch was created with Al Noor Fuel & Lubricants LLC selected. The portal rejected the invoices with error AE-BR-10 because the seller TRN did not match the selected company.
>
> The batch summary shows all 40 invoices rejected by portal validation. The inspected invoices have no ASP reference or ASP response, so they were not confirmed as submitted to the ASP.
>
> Please do not resend the batch yet. We will confirm the correct company configuration and safe re-upload procedure before proceeding. We also need to reconcile the portal upload timestamp with the date in your report.
>
> Regards,  
> Product Support Team

### Workaround offered

- Hold further connector retries and manual resubmissions.
- Verify that Sahara Medical Supplies FZE is configured correctly and that its seller TRN matches the source file.
- After checking invoice statuses and duplicate risk, arrange a controlled re-upload using the correct company.
- Verify ASP references and buyer delivery status after resubmission.

### Prevention ideas

- Validate the selected company against the seller TRN before accepting a batch.
- Display a clear warning when the seller identity in the file does not match the selected company.
- Prevent automatic connector retries for batches rejected by portal validation.
- Provide per-invoice processing outcomes to the connector rather than relying only on HTTP 201.
- Improve batch timestamps and status visibility so connector and portal records can be reconciled.

--- `

## Incident 5 — `inbox/05-ghaf-cafe-date-error.md`

### Acknowledgement

- **Draft prepared at:** 12:15 GST, 16 Nov 2026 (not sent to a real customer)
- **Reply:**

> Hi Hessa,
>
> Thank you for contacting us. I understand how frustrating this is, especially when you need to issue invoices to your customers.
>
> The date you entered, 13 November 2026, is a valid calendar date. However, our upload template requires dates in YYYY-MM-DD format. In your case, this should be 2026-11-13.
>
> I also found a seller company/TRN mismatch in the uploaded batch. I am checking both issues so that we can give you the correct steps without risking duplicate invoices.
>
> Please hold off on uploading the batch again until we confirm the safe next step. I will provide an update by 16:10 GST.
>
> Regards,  
> Product Support Team

### Priority and justification

- **Priority:** P2
- **Why:** All four invoices in this customer's batch were rejected. The inspected invoice shows both date-format validation errors and a seller TRN mismatch. This is a batch-level rejection affecting the customer's invoicing workflow, but there is no evidence of a wider production outage.
- **SLA deadlines I am working to:**
  - First response: within 1 hour of receipt, by 13:10 GST on 16 Nov 2026.
  - Resolution target: 8 business hours. Since business hours end at 18:00 GST, the remaining 2 hours and 10 minutes continue from 08:00 on 17 Nov; target is 10:10 GST on 17 Nov 2026.
  - Updates: every 2–4 hours; next update due between 14:10 and 16:10 GST.

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 12:15 GST | Reviewed the customer report | Customer reported all invoices rejected because the issue date was entered as 13/11/2026 |
| Not recorded | Opened invoice GTC-118 in the portal | Rejected by portal validation; ASP reference empty |
| Not recorded | Reviewed GTC-118 validation errors | AE-BR-04 for issue date and payment due date on rows 2 and 3 |
| Not recorded | Reviewed the same invoice's seller validation | AE-BR-10: seller TRN in the file does not match the selected company |
| Not recorded | Reviewed batch summary | Four invoices, eight source rows, all four invoices rejected |
| Not recorded | Reviewed source rows in the batch | Seller is Ghaf Tree Café LLC; selected company is Al Noor Fuel & Lubricants LLC |
| Not recorded | Checked batch timestamps | Portal records upload on 14 Nov 2026 at 17:58:54, while customer email is dated 16 Nov 2026. This discrepancy needs clarification |

### Root cause

- **What happened:** The batch was rejected by portal validation. The inspected invoice GTC-118 contains dates in DD/MM/YYYY format, while the portal requires YYYY-MM-DD. It also contains a seller TRN that does not match the selected company.
- **Where:** Spreadsheet upload and portal validation. Exact source code location not yet verified.
- **Why it happens:** The uploaded dates use a format that fails the portal's date validation rule AE-BR-04. Separately, the source seller is Ghaf Tree Café LLC, but the selected company is Al Noor Fuel & Lubricants LLC, triggering AE-BR-10.
- **Evidence:**
  1. `evidence/incident-5-gtc-118-validation-errors.png` — invoice GTC-118 with date and seller TRN validation errors.
  2. `evidence/incident-5-batch-summary.png` — batch summary showing four invoices and all four rejected.
  3. Batch source rows show Ghaf Tree Café LLC and seller TRN `100754862300003`.
- **Confidence and open questions:** High confidence in both validation failures for GTC-118. The batch summary confirms all four invoices were rejected, but individual error details for GTC-119, GTC-120 and GTC-121 have not been inspected. The 14 Nov portal timestamp versus 16 Nov customer report also needs clarification.

### Customer resolution reply OR escalation note

- **Which one and why:** Customer resolution reply draft. The inspected invoice provides clear validation errors and does not currently require engineering escalation.

> Hi Hessa,
>
> We checked invoice GTC-118 and found two issues.
>
> First, the portal requires dates in YYYY-MM-DD format. Your date 13/11/2026 is valid, but it needs to be entered as 2026-11-13. The due date 13/12/2026 should be entered as 2026-12-13.
>
> Second, the uploaded file identifies Ghaf Tree Café LLC as the seller, but the portal batch was created with Al Noor Fuel & Lubricants LLC selected. This causes a seller TRN validation error.
>
> The batch summary shows that all four invoices were rejected. Please do not upload the batch again yet. We recommend confirming the correct company selection and correcting the date formats before a controlled re-upload. We will confirm the safe process before you retry.
>
> Regards,  
> Product Support Team

### Workaround offered

- Enter issue and due dates in YYYY-MM-DD format.
- Verify that Ghaf Tree Café LLC is selected and its seller TRN matches the uploaded file.
- Do not retry or resend the batch until the invoice statuses and duplicate risk have been checked.
- Perform a controlled re-upload only after both validation issues are corrected.

### Prevention ideas

- Provide an Excel template with an explicit date-format example such as `2026-11-13`.
- Add pre-upload date-format validation with a clear correction message.
- Validate selected company against seller name and TRN before processing.
- Show a clear warning when the uploaded seller does not match the selected company.
- Provide simple, non-technical correction instructions for small-business customers.

---


### Overall prioritisation

1. **Incident 2 – Marina Bay API restart (provisional P2):** Highest urgency for further impact validation because API restarts, intermittent 502 errors and invoices stuck in Validated status were reported. If production invoice submission to the ASP is confirmed down, upgrade to P1 and page Engineering immediately.
2. **Incident 1 – Oasis Builders (P2):** Eight invoices were rejected, affecting month-end invoicing. The company-selection and seller-TRN mismatch is identified.
3. **Incident 4 – Bluepeak Sahara (P2):** All 40 invoices in the batch were rejected before confirmed ASP submission, affecting the customer's payment workflow.
4. **Incident 5 – Ghaf Tree Café (P2):** All four invoices in the batch were rejected due to validation issues, affecting the customer's invoicing.
5. **Incident 3 – Al Noor Fuel (P2):** Three of six invoices were rejected by the ASP due to BR-CO-15. The payload mismatch is confirmed for invoice 0042; the exact code-level cause remains open.

I would reassess priorities whenever new evidence changes the customer impact. P1 incidents take precedence and require immediate Engineering escalation. For all incidents, I would keep the customer informed according to the SLA while retaining L1 ownership.

### What I would do differently

- I would verify the selected company and seller TRN before investigating repeated upload failures.
- I would check individual invoice statuses and ASP references instead of relying only on the batch-level status.
- I would preserve timestamps, request IDs, invoice IDs and relevant log excerpts during investigation.
- For API restart alerts, I would first establish customer impact and whether invoices are reaching the ASP before deciding the final priority.
- I would avoid repeated retries until the invoice status and duplicate risk are confirmed.
- I would communicate clear next-update times and keep ownership of customer communication even when Engineering is involved.


### What I learned

- An HTTP 201 response confirms request acceptance, not successful invoice delivery to the buyer.
- Invoice-level status and ASP references are essential for confirming successful submission.
- Seller TRN and selected-company mismatches can reject an entire batch before ASP submission.
- BR-CO-15 requires invoice totals to reconcile correctly: tax-exclusive amount plus VAT must equal tax-inclusive amount.
- Date formats must follow the portal's specified format, even when the calendar date is valid.
- Monitoring alerts must be correlated with customer impact and historical logs before confirming a root cause.
- When evidence is incomplete, documenting uncertainty is better than guessing.
- Clear acknowledgement, realistic update commitments and continued L1 ownership are essential in support operations.

### Real-time work log

| Activity | Estimated time spent |
|---|---:|
| Environment setup and understanding the assignment | 30 minutes |
| Incident 1 investigation and documentation | 1 hour |
| Incident 2 investigation and documentation | 1 hour |
| Incident 3 investigation and documentation | 1 hour |
| Incident 4 investigation and documentation | 1 hour |
| Incident 5 investigation and documentation | 45 minutes |
| Evidence collection and submission review | 45 minutes |
| **Total** | **6 hours** |

### AI usage disclosure

I used ChatGPT as an assistance tool to understand the assignment requirements, organise investigation notes, interpret technical findings and improve the clarity of customer communication drafts.

I reviewed the available application data, invoice details, monitoring results, logs and code references to validate the findings. I treated unverified causes as unknown and documented evidence gaps rather than presenting assumptions as confirmed facts.

AI assistance was used for drafting and analysis support. The final investigation notes, evidence selection and submission review are my responsibility.