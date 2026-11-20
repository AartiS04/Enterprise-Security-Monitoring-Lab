# SOC Investigation #001 — Failed Windows logons

**Goal:** Detect unsuccessful interactive logons and correlate repeated failures.

- **Data source:** Windows Security Event ID **4625**.
- **Native alert:** Wazuh Rule **60122**, Level 5, unknown user or bad password.
- **Custom detection:** Rule **100100**, Level 10; five matching events within 60 seconds; MITRE ATT&CK **T1110**.
- **Simulation:** Controlled logon attempts using a nonexistent Windows local account, `SOC-Test-Nonexistent`.
- **Finding:** Multiple Event ID 4625 records and the custom Rule 100100 alert appeared in Wazuh.
- **Conclusion:** Detection validated; authorized benign test, not proof of an actual brute-force attack.

The custom rule uses a broad frequency threshold without per-user or per-source aggregation. This is a tuning limitation.

See [`rule-100100.xml`](../detection-rules/rule-100100.xml) and the private source report for the full evidence.
