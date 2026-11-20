# SOC Investigation #003 — Wireshark and Sysmon network connections

**Goal:** Capture and interpret traffic and associate network events with Windows process context.

## Wireshark findings

- `nslookup example.com`: DNS query/response visible.
- `ping -n 4 1.1.1.1`: ICMP Echo Requests and Replies visible.
- `Test-NetConnection example.com -Port 443`: TCP connection activity observed; a partial capture included FIN/ACK, duplicate ACK and retransmission traffic. Do not claim a complete three-way handshake unless all three initial packets were captured.
- Protocol Hierarchy and Conversations views inspected; evidence retained in private screenshots.

Useful display filters: `dns`, `icmp`, `tcp.flags.syn == 1`, `tcp.stream == <stream-id>`.

## Sysmon / Wazuh

- Enabled Sysmon Event ID **3** network connection logging, scoped to `powershell.exe`, while preserving Event ID 1.
- The Windows agent forwarded events from `Microsoft-Windows-Sysmon/Operational`.
- Event ID 3 records with `powershell.exe`, `tcp`, and destination port `443` were confirmed in Wazuh `archives.json`, decoded as `windows_eventchannel`.
- Custom Rule **100102** was defined and syntax validated but **never produced an observed alert**, including after parent/group adjustments. `alerts.json` and the Dashboard had no matching alert.
- This demonstrates **successful telemetry collection, not a validated alert**. A possible low-level rule-chain limitation remains unverified.

Note: Raw JSON event archiving was enabled temporarily for diagnostics. It should be disabled after evidence collection to avoid excessive storage use.
