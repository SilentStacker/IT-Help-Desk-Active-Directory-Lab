# Ticket #001 — Password Reset & Account Unlock

## Ticket Info
| Field | Details |
|---|---|
| Ticket ID | TKT-001 |
| Priority | Medium |
| Status | Resolved |
| Assigned To | Jamari James |

## Issue Reported
User Alice Smith (asmith@homelab.local) reported she was locked
out of her account after multiple failed login attempts and could
not access her workstation.

## Steps Taken
1. Received ticket via osTicket help desk portal
2. Verified user identity before making any changes
3. Opened Active Directory Users & Computers on DC01
4. Located user account: ABC Tech OU → Alice Smith
5. Right-clicked account → Properties → Account tab
6. Confirmed account was locked — checked "Unlock Account"
7. Reset password to temporary: `Temp@1234!`
8. Checked "User must change password at next logon"
9. Communicated new credentials to user securely
10. Confirmed user successfully logged in

## Root Cause
User exceeded the account lockout threshold defined in the
Default Domain Policy GPO (5 failed attempts).

## Resolution
Account unlocked and password reset via ADUC. User logged in
successfully and changed password at next logon.

## Commands Used
```powershell
# Check account lockout status
Search-ADAccount -LockedOut | Select Name, SamAccountName

# Unlock account via PowerShell
Unlock-ADAccount -Identity "asmith"

# Force password reset
Set-ADAccountPassword -Identity "asmith" -Reset `
-NewPassword (ConvertTo-SecureString "Temp@1234!" -AsPlainText -Force)

Set-ADUser -Identity "asmith" -ChangePasswordAtLogon $true
```

## Outcome
✅ Account unlocked and access restored within 10 minutes of
ticket submission.
