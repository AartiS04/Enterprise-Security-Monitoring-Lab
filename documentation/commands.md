# Command reference

Commands are provided for reproduction **only on authorized lab systems**. Do not replace working production configuration files with the examples in this repository.

## Wazuh (Ubuntu)

```bash
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
sudo /var/ossec/bin/wazuh-analysisd -t
sudo grep -nE 'id="100100"|id="100101"|id="100102"' /var/ossec/etc/rules/local_rules.xml
```

## Windows agent and Sysmon (PowerShell)

```powershell
Get-Service WazuhSvc
Get-Service Sysmon64,Sysmon -ErrorAction SilentlyContinue
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational';Id=1;StartTime=(Get-Date).AddMinutes(-10)} -ErrorAction SilentlyContinue | Select-Object -First 5 TimeCreated,Id,Message
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational';Id=3;StartTime=(Get-Date).AddMinutes(-10)} -ErrorAction SilentlyContinue | Select-Object -First 5 TimeCreated,Id,Message
powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB-TEST-002'"
Test-NetConnection example.com -Port 443
```

## Wireshark traffic generation

```powershell
nslookup example.com
ping -n 4 1.1.1.1
Test-NetConnection example.com -Port 443
```

Display filters: `dns`, `icmp`, `tcp`, `tcp.flags.syn == 1`, and `tcp.stream == 0` (replace `0` with the observed stream index).

## Ubuntu Nmap / network exposure

```bash
nmap -sT -Pn -F 127.0.0.1 -oN baseline-localhost.txt
nmap -sT -Pn -p 22,80,443,1514,1515,55000,9200 127.0.0.1 -oN wazuh-local-ports.txt
nmap -sT -sV --version-light -Pn -p 22,443,1514,1515,55000,9200 127.0.0.1 -oN wazuh-service-versions.txt
sudo ss -lntp
sudo ufw status verbose
```

From your Windows host, set `$vmIP` to **your own VM address** and test only your authorized lab ports:

```powershell
$vmIP = 'YOUR_VM_IP'
$ports = 22,80,443,1514,1515,55000,9200
foreach ($port in $ports) {
  $r = Test-NetConnection -ComputerName $vmIP -Port $port -WarningAction SilentlyContinue
  [PSCustomObject]@{ Port=$port; Reachable=$r.TcpTestSucceeded }
}
```

## Investigation-only raw archiving

The Wazuh `logall_json` setting was temporarily enabled to verify receipt of Sysmon Event ID 3 in `archives.json`. Disable it again after diagnosis to conserve disk space. Never publish the raw archives.
