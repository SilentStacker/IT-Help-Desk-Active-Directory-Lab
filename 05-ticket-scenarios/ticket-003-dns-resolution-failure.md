# Ticket #003 — DNS Resolution Failure

## Ticket Info
| Field | Details |
|---|---|
| Ticket ID | TKT-003 |
| Priority | High |
| Status | Resolved |
| Assigned To | Jamari James |

## Issue Reported
Windows 11 client (HomelabV1) could not resolve domain names
after a network change. User reported they could not reach
internal resources or log into domain-joined applications.

## Steps Taken
1. Attempted ping to domain controller by hostname — failed
2. Attempted ping to DC by IP (192.168.122.10) — succeeded
3. Confirmed issue was DNS resolution, not connectivity
4. Checked DNS settings on client NIC:
   - Run → ncpa.cpl → Ethernet Properties → IPv4
   - DNS server was set to wrong IP after network change
5. Updated DNS to point to DC IP: 192.168.122.10
6. Flushed DNS cache on client:
   `ipconfig /flushdns`
7. Ran nslookup to verify resolution:
   `nslookup homelab.local`
8. Confirmed hostname ping now successful
9. Verified domain login working correctly

## Root Cause
Client NIC DNS setting was pointing to incorrect IP after a
network configuration change on the host machine. DC was
unreachable by hostname causing all domain services to fail.

## Resolution
Updated DNS server address on client NIC to correct DC IP.
Flushed DNS cache. Domain resolution restored immediately.

## Commands Used
```powershell
# Flush DNS cache
ipconfig /flushdns

# Test DNS resolution
nslookup homelab.local

# Check current DNS settings
Get-DnsClientServerAddress

# Set DNS via PowerShell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" `
-ServerAddresses "192.168.122.10"
```

## Outcome
✅ DNS resolution restored within 20 minutes. Client confirmed
able to reach all domain resources and log in successfully.
