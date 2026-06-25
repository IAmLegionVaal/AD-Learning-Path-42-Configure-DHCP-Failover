# AD Learning Path 42 — Configure DHCP Failover

## Goal
Create a supported DHCP failover relationship between two authorized lab DHCP servers and verify scope, lease, and partner-state synchronization.

## Prerequisites
- Two domain-joined DHCP servers authorized in AD
- DNS, time, and network connectivity between the servers
- An existing lab IPv4 scope on `SRV01`
- No duplicate standalone scope on `SRV02`

## Steps
1. Open the DHCP console on `SRV01`.
2. Right-click the lab IPv4 scope and select **Configure Failover**.
3. Select `SRV02.corp.lab` as the partner.
4. Name the relationship `AD-LAB-FO`.
5. Choose Load Balance mode and a 50/50 distribution for the lab.
6. Set the Maximum Client Lead Time to one hour.
7. Complete the relationship using the organization's secure configuration process.
8. Confirm the scope appears on both servers.
9. Renew a disposable client while both servers are online.
10. Perform a controlled availability test, restore both services, and confirm the relationship returns to Normal.

## Validation
```powershell
Get-DhcpServerv4Failover -ComputerName SRV01
Get-DhcpServerv4Failover -ComputerName SRV02
Get-DhcpServerv4Lease -ComputerName SRV01 -ScopeId 10.10.10.0
Get-DhcpServerv4Lease -ComputerName SRV02 -ScopeId 10.10.10.0
Get-WinEvent -LogName 'Microsoft-Windows-DHCP-Server/Operational' -MaxEvents 100
```

## Expected result
Both servers show the same relationship and scope, lease information is synchronized, the test client can renew during the controlled availability test, and the final partner state is Normal.

## Evidence
Under `evidence/`, store relationship properties from both servers, scope and lease checks, controlled availability-test output, return-to-normal events, errors/remediation, and final pass/fail status. Do not record sensitive relationship values.

## Troubleshooting
- Partner communication fails: validate DNS, time, firewall, and DHCP failover connectivity.
- Scope conflict: remove or redesign the conflicting scope before retrying.
- Relationship remains degraded: inspect both partner states and the DHCP operational logs.

## Cleanup
Remove the relationship only after selecting which server will retain the scope and confirming client impact.

## Next activity
`AD-Learning-Path-43-Add-an-Additional-Domain-Controller`
