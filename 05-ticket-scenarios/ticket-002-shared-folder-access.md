# Ticket #002 — Shared Folder Access Denied

## Ticket Info
| Field | Details |
|---|---|
| Ticket ID | TKT-002 |
| Priority | High |
| Status | Resolved |
| Assigned To | Jamari James |

## Issue Reported
User Bob Bo (bbo@homelab.local) reported receiving "Access Denied"
when attempting to open the CompanyData shared folder mapped as
Z: drive on his workstation.

## Steps Taken
1. Confirmed Z: drive was mapped and visible in File Explorer
2. Attempted to open folder — received "Access Denied" error
3. Checked share permissions on DC01:
   - Right-clicked CompanyData → Properties → Sharing → Advanced
   - Confirmed HR Dept group had Read/Write but IT dept was missing
4. Checked NTFS permissions on the folder:
   - Security tab confirmed IT dept group had no permissions set
5. Added IT dept security group to both Share and NTFS permissions
6. Set NTFS permission to Modify for IT dept group
7. Asked user to log off and log back on to refresh token
8. Confirmed user could access Z: drive successfully

## Root Cause
User was a member of IT dept security group but that group had
not been granted permissions on the CompanyData share or NTFS
folder level.

## Resolution
Added IT dept group to share and NTFS permissions with Modify
access. User confirmed access restored after re-login.

## Commands Used
```powershell
# Check user group membership
Get-ADUser -Identity "bbo" -Properties MemberOf | 
Select -ExpandProperty MemberOf

# Check who has access to shared folder
Get-SmbShareAccess -Name "CompanyData"

# Add group to share permissions
Grant-SmbShareAccess -Name "CompanyData" `
-AccountName "homelab\IT dept" -AccessRight Full -Force
```

## Outcome
✅ Access restored within 15 minutes. Share and NTFS permissions
updated and documented for future reference.
