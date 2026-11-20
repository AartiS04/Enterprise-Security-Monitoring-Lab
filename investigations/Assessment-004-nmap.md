# Assessment #004 — Nmap service and exposure baseline

**Scope:** Only the author's Ubuntu Wazuh VM and connectivity from the author's Windows host.

## Observations

| TCP port | Localhost | Windows host reachability | Purpose / context |
| --- | --- | --- | --- |
| 22 | Open | Yes | OpenSSH (Nmap identified OpenSSH 9.6p1 Ubuntu) |
| 80 | Closed | No | HTTP not listening in tested scope |
| 443 | Open | Yes | HTTPS / Wazuh Dashboard |
| 1514 | Open | Yes | Wazuh agent transport |
| 1515 | Open | Yes | Wazuh agent enrollment |
| 55000 | Open | Yes | Wazuh management API |
| 9200 | Open on localhost | No | Wazuh Indexer bound to loopback |

`sudo ufw status verbose` reported **inactive**. `sudo ss -lntp` showed ports **22, 443, 1514, 1515, and 55000** listening on network interfaces; **9200** was bound to loopback. Nmap's uncertain service labels on nonstandard ports were not treated as confirmed identities.

## Hardening recommendations

1. Plan firewall allowlisting before changing any access rules; preserve agent and dashboard connectivity.
2. Limit Wazuh API / Dashboard access to trusted administrative endpoints.
3. Restrict agent enrollment to intended endpoints and review whether enrollment must remain continuously available.
4. Review SSH authentication and accepted sources.
5. Keep Indexer bound to loopback and verify it remains inaccessible from the Windows host.

**Limitations:** This was a baseline service-exposure review. No exploitable CVE, vulnerability exploitation or completed remediation verification was demonstrated.
