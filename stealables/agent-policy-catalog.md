# Starter Agent Policy Catalog

**Who it's for**  
Revenue operator or enablement manager at a 1–3 person pre‑IPO team who needs to know *exactly* which agents write to the CRM, when a human must review, and how to hand the policy off intact.

---

## The catalog

| Use Case | Data Source | CRM Truth | Signal Feed | Human Gate? | Write Rule |
|----------|-------------|-----------|-------------|-------------|------------|
| Lead capture | Form POST | Lead record | Scoring model | No | `lead.owner = agent`<br>`lead.status = "New"` |
| Opportunity qual | Opp form | Opp record | Stage history | **Yes** | `if stage == "Qualified"`<br>`  lead.status = "Qualified"` |
| Deal close | Close form | Deal record | Won value | **Yes** | `if deal.value > 0`<br>`  opp.status = "Closed Won"` |
| Account refresh | Account sync | Account record | Health snapshot | No | `account.owner = agent`<br>`account.status = "Active"` |

> **Mark each yes/no now:**  
> 1. Does your CRM schema match the field names in column six?  
> 2. Do you have write credentials scoped to *only* those fields?  
> 3. Can a manager approve the human‑gate rows in Slack in under two minutes?  
> 4. Is the YAML below committed to version control?  
> 5. Does one person own breaking changes to this catalog?  
> 6. Can the next ops hire read this table and ship a policy update on day one?

---

## Sample YAML

```yaml
# agent_policies.yml – EXAMPLE VALUES ONLY
policies:
  - name: lead_capture
    data_source: lead_form
    crm_truth: lead_db
    signal_feed: lead_scoring
    human_gate: false
    write_rules:
      - "lead.owner = agent"
      - "lead.status = 'New'"

  - name: opportunity_qual
    data_source: opp_form
    crm_truth: opp_db
    signal_feed: stage_history
    human_gate: true
    write_rules:
      - "if (opp.stage == 'Qualified') { lead.status = 'Qualified' }"

  - name: deal_close
    data_source: close_form
    crm_truth: deal_db
    signal_feed: won_value
    human_gate: true
    write_rules:
      - "if (deal.value > 0) { opp.status = 'Closed Won' }"
```

*Numbers, field names, and threshold logic are EXAMPLE placeholders.*

---

## Monday Motion

1. **Audit** – Open the YAML; confirm each policy still matches production CRM field names.  
2. **Flag** – If a human‑gate policy fired more than *EXAMPLE: 10 times* last week, add a pre‑filter rule.  
3. **Commit** – Push the updated YAML and tag the ops channel so the next sprint inherits clean policy.

---

## Related

- [Assessment](/assessment)  
- [Start Here](/start-here)  
- [Enabling Managers](/enabling-leaders/enabling-managers)  
- [Enablement Operating Model](/enabling-leaders/enablement-operating-model)  
- [Build the System](/build-the-system)

---

**Disclosure**  
This catalog is a starting template. Validate write‑rule syntax against your CRM's API documentation before committing to production. Always test policy changes in a sandbox instance first.
