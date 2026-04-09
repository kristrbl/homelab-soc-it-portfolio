# Incident Report

| Field | Value |
|---|---|
| Report Date | [YYYY-MM-DD] |
| Incident ID | INC-[YYYYMMDD]-[###] |
| Analyst | kristrbl |
| Severity | [Critical / High / Medium / Low / Informational] |
| Status | [Open / In Progress / Closed] |
| System Affected | [hostname or IP] |

---

## Summary

[2-3 sentence plain-language summary of what happened, when it was detected, and current status.]

---

## Timeline

| Time (UTC) | Event |
|---|---|
| HH:MM | [First indicator observed / alert triggered] |
| HH:MM | [Investigation opened] |
| HH:MM | [Key finding] |
| HH:MM | [Containment action taken] |
| HH:MM | [Investigation closed / escalated] |

---

## Detection

**Alert Source:** [SIEM / IDS / Manual review / User report]
**Rule / Signature:** [Rule ID or name]
**Alert Level:** [1-15 for Wazuh / Low-High for others]

---

## Investigation

**What was detected:**
[Describe the alert and raw log data]

**What was verified:**
[What you checked to confirm or rule out the alert]

**Affected systems:**
- [hostname / IP]

**Accounts involved:**
- [username]

**Source IP(s):**
- [IP]

---

## Root Cause

[What caused this alert to fire? Is it a true positive, false positive, or benign positive?]

| Assessment | Reasoning |
|---|---|
| [True Positive / False Positive / Benign Positive] | [explanation] |

---

## MITRE ATT&CK Mapping *(if applicable)*

| Field | Value |
|---|---|
| Tactic | [e.g., Credential Access] |
| Technique | [e.g., T1110 — Brute Force] |
| Sub-technique | [e.g., T1110.001 — Password Guessing] |

---

## Containment & Response Actions Taken

- [ ] [Action 1 — e.g., Account locked]
- [ ] [Action 2 — e.g., Source IP blocked]
- [ ] [Action 3 — e.g., System owner notified]

---

## Recommendations

1. [Preventive or hardening recommendation]
2. [Detection improvement]
3. [Process improvement]

---

## Disposition

[Closed / Escalated to Tier 2 / Referred to IR team]

**Closing notes:**
[Final summary of outcome]

---

*Report author: kristrbl | [date]*
