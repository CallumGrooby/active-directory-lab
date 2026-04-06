# Troubleshooting

Using my Active Directory (AD) home lab, I have been practising troubleshooting some common issues that come up inside AD networks, documenting each one as I would in a real helpdesk environment.

## Key Objectives

- Troubleshoot and fix common issues found on the network
- Practice raising and resolving tickets using a ticketing system
- Document root causes and resolutions clearly

## Common Issues

### Ticket #001 - Jordan P: Account Locked Out / Password Reset

**Reported Issue:**
User reported they were unable to log into their machine, receiving an account locked out error when attempting to sign in.

**Root Cause:**
The user had entered their password incorrectly multiple times, triggering the account lockout policy and locking their Active Directory account.

**Resolution:**
Located the user account in Active Directory Users and Computers. Unlocked the account via the Account tab and reset the password to a temporary one with User must change password at next logon enabled. Confirmed with the user that they were able to log in successfully and set their own new password.

| Ticket                                              | Finding the User                                                  | Target Action                                             | Unlocking Account                                                 |
| --------------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------- |
| ![Ticket 001](images/Ticket%20001/Ticket%20001.png) | ![Finding the user](images/Ticket%20001/Finding%20the%20user.png) | ![Target Action](images/Ticket%20001/Target%20Action.png) | ![Unlocking account](images/Ticket%20001/Unlocking%20account.png) |

---

### Ticket #002 - Chris T: Staff Drive Not Mapping

**Reported Issue:**
User reported that the Staff shared drive was not appearing on their machine after logging in.

**Root Cause:**
The GPO drive mapping was set up incorrectly. The item-level targeting groups had been typed manually rather than selected from Active Directory, causing them to fail to resolve silently. Additionally the NTFS security permissions on the shared folder did not include the required groups, and the preference item was not configured to run in the logged-on user's security context.

**Resolution:**
Updated the item-level targeting to use verified AD group names selected via Check Names. Added the relevant groups to the NTFS security permissions on the Staff shared folder. Enabled Run in logged-on user's security context on the preference item and changed the action to Update. Ran `gpupdate /force` on the client machine and confirmed the N: drive mapped successfully on next login.

| Ticket                                              | Updating the Drive Action                                                             | Verifying                                        |
| --------------------------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------ |
| ![Ticket 002](images/Ticket%20002/Ticket%20002.png) | ![Updating the drive action](images/Ticket%20002/Updating%20the%20drive%20action.png) | ![Verifying](images/Ticket%20002/Verfifying.png) |

---

### Ticket #003 - Mark K: Staff Folder Access Denied

**Reported Issue:**
User could see the Staff shared drive mapping but was unable to access it, receiving an access denied error when attempting to open it.

**Root Cause:**
The Marketing group had not been granted permissions on the Staff shared folder. The drive was visible due to the GPO applying correctly, but the NTFS security permissions were blocking access for any user in the Marketing group.

**Resolution:**
Added the Marketing group to the Staff folder NTFS security permissions with Read and Change access. Verified the fix by refreshing File Explorer on the client machine and confirming the user was able to open and access the folder successfully.

| Ticket                                              | Added Marketing Group                                                       |
| --------------------------------------------------- | --------------------------------------------------------------------------- |
| ![Ticket 003](images/Ticket%20003/Ticket%20003.png) | ![Added Marketing Group](images/Ticket%20003/Added%20Marketing%20Group.png) |

---

### Ticket #004 - Mark K: Wallpaper Not Applying

**Reported Issue:**
User's desktop wallpaper was not updating to the company wallpaper after the GPO was configured.

**Root Cause:**
The Background Image GPO did not have the correct groups added to its security filtering, so it was not being assigned to the Marketing department. Additionally the wallpaper image was stored in an incorrect location on the server, meaning that when the GPO did apply the desktop background appeared as a black screen rather than the intended image.

**Resolution:**
Updated the Background Image GPO security filtering to include IT Staff, Marketing, and Sales, and ensured each group had Read access via the Delegation tab. Moved the wallpaper image into `\\lab.local\SYSVOL\lab.local\` to ensure it is accessible by all domain users. Confirmed the wallpaper applied correctly after the user logged off and back on.

| Ticket                                              | Background Image                                                | GPO                                 |
| --------------------------------------------------- | --------------------------------------------------------------- | ----------------------------------- |
| ![Ticket 004](images/Ticket%20004/Ticket%20004.png) | ![Background Image](images/Ticket%20004/Background%20Image.png) | ![GPO](images/Ticket%20004/GPO.png) |
