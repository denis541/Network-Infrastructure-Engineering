# Troubleshooting — Device Hardening Lab

## Issue 1: VTY and Console passwords not being accepted

**Symptom:**
After setting passwords on VTY lines and console line 0, the device was not
prompting for a password or was rejecting login attempts.

**Root Cause:**
The `login` command was not configured on the lines. Without `login local`
or `login`, IOS does not enforce password authentication even if a password
is set.

**Fix:**
Added `login local` to both VTY lines and console line 0, and created a
local username with a secret:

    username admin secret <password>

    line console 0
     login local

    line vty 0 4
     login local

**Verification:**
Tested by logging out and back in — device prompted for username and password
on both console and VTY access.

---

## Issue 2: Passwords visible in plain text

**Symptom:**
Running `show running-config` revealed passwords in plain text, which is a
security risk.

**Root Cause:**
Passwords were set using the `password` command instead of `secret`, and
`service password-encryption` was not enabled.

**Fix:**
Replaced all plain text passwords with encrypted secrets using:

    enable secret <password>
    username admin secret <password>

Also enabled global password encryption for any remaining plain text entries:

    service password-encryption

**Verification:**
Ran `show running-config` and confirmed all passwords appeared as encrypted
hashes instead of plain text.
