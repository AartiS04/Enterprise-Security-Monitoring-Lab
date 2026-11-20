# SOC Investigation #002 — Sysmon PowerShell process creation

**Goal:** Detect a specific, benign PowerShell process creation command.

- **Telemetry:** `Microsoft-Windows-Sysmon/Operational`, Sysmon Event ID **1**.
- **Native Wazuh alert:** **92027**, Level 4, PowerShell spawning a PowerShell instance.
- **Test command:** `powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB-TEST-002'"`.
- **Custom detection:** **100101**, Level 7, PowerShell executable and unique marker in the command line.
- **Finding:** Event metadata recorded the executable, command line, parent process and user context. The custom Rule 100101 alert appeared in Wazuh.
- **Conclusion:** Alert validation successful; the test command only printed a marker and was not malicious.

See [`rule-100101.xml`](../detection-rules/rule-100101.xml).
