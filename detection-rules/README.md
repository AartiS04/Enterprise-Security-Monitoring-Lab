# Wazuh detection rules

These are **documented example rule blocks**, extracted from the guided lab setup. They are not a full replacement for `/var/ossec/etc/rules/local_rules.xml`.

- **100100:** Validated repeated failed-logon correlation.
- **100101:** Validated PowerShell marker process creation.
- **100102:** **Unresolved** Sysmon Event ID 3 alert. Kept as a reference only; not shown as a validated detector.

Before testing, back up existing rules; check for conflicting rule IDs, validate with `/var/ossec/bin/wazuh-analysisd -t`, and restart `wazuh-manager` only after validation.
