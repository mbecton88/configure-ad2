# Lab: Building Your Own Azure Active Directory Domain

**Objective:**
Create a domain controller, join a client machine, and configure security policies to simulate a real-world network environment with security best practices.

**Why?**
We need to create a domain controller, join a client machine, and configure security policies to simulate a real-world network environment with security best practices.

---

## 1. Install Active Directory Domain Services (AD DS)

* **Action:** Log into "DC-1" via RDP as the local "labuser."
* **Action:** Open "Server Manager" -> "Add roles and features."
* **Action:** Select "Active Directory Domain Services."
  ![image](https://github.com/user-attachments/assets/e836c047-6125-452d-8919-825ade2bd309)
* **Explanation:** This installs the necessary components for Active Directory.


---

## 2. Promote "DC-1" to a Domain Controller

* **Action:** After installation, click "Promote this server to a domain controller."
  ![Screenshot 2025-03-09 at 8 56 40 PM](https://github.com/user-attachments/assets/673c74b8-9691-454b-a839-3eef335351fb)
* **Action:** Choose "Add a new forest."
* **Action:** Domain name: "mydomain.com" (or something unique).
* **Action:** Restart "DC-1."
* **Action:** Log back in as "mydomain.com\labuser."
* **Explanation:** This creates a new domain.


---

## 3. Configure Account Lockout Policy

* **Action:** In the search box, type (gpmc.msc) to open "Group Policy Management".
* **Action:** Expand Forest: mydomain.com, Domains, mydomain.com.
* **Action:** Right click the "Default Domain Policy" and select "Edit."
  ![image](https://github.com/user-attachments/assets/21bde3ae-c669-4ffe-8a68-14fc4825cdf7)
* **Action:** Expand "Computer Configuration", Policies, Windows Settings, Security Settings, Account Policies, Account Lockout Policy.
* **Action:** Configure:
    * Account lockout duration: 30 minutes
    * Account lockout threshold: 3 invalid logon attempts
    * Reset account lockout counter after: 30 minutes
* **Action:** Close the Group Policy Management Editor.
* **Action:** Run `gpupdate /force` in command prompt.
* **Explanation:** This policy increases security by locking out accounts after a certain number of failed login attempts.
* **Screenshot Hint:** Capture a screenshot of the Account Lockout Policy settings.

---

## 4. Create a Domain Admin User

* **Action:** In the search box, type (ADUC) open "Active Directory Users and Computers."
* **Action:** Create OUs: "_EMPLOYEES" and "_ADMINS."
* **Action:** Create user "Jane Doe" (username: "jane_admin," password: "Cyberlab123!").
* **Action:** Add "jane_admin" to "Domain Admins."
* **Action:** Log out and log back in as "cyberlab.com\jane_admin."
* **Explanation:** This creates a dedicated admin account.
* **Screenshot Hint:** Capture screenshots of the created OUs and the "jane_admin" user's properties.

---

## 5. Join Client-1 to the Domain

* **Action:** Ensure "Client-1's" DNS settings point to "DC-1's" private IP address (from previous lab).
* **Action:** Restart "Client-1" from the Azure portal.
* **Action:** Log in to "Client-1" as the local "labuser."
* **Action:** Open "System Properties" -> "Computer Name" -> "Change."
* **Action:** Select "Domain" and enter "cyberlab.com."
* **Action:** Enter "cyberlab.com\jane_admin" and "Cyberlab123!" when prompted.
* **Action:** Restart "Client-1."
* **Action:** Log in to "DC-1" and verify "Client-1" is in ADUC.
* **Action:** Create an OU named "_CLIENTS" and move "Client-1" into it.
* **Explanation:** This joins the client machine to the domain.
* **Screenshot Hint:** Capture a screenshot of the System Properties window after joining the domain and a screenshot of ADUC showing Client-1 in the _CLIENTS OU.

---

## 6. Enable Remote Desktop for Domain Users

* **Action:** Log in to "Client-1" as "cyberlab.com\jane_admin."
* **Action:** Open "System Properties" -> "Remote Desktop."
* **Action:** Select "Allow remote connections to this computer."
* **Action:** Click "Select Users" and add "Domain Users."
* **Explanation:** This allows domain users to remotely access the client machine.
* **Screenshot Hint:** Capture a screenshot of the Remote Desktop settings with "Domain Users" added.

---

## 7. Create Multiple Users

* **Action:** Log in to "DC-1" as "cyberlab.com\jane_admin."
* **Action:** Open "PowerShell ISE" as administrator.
* **Action:** Create a new file and paste the following script:

    ```powershell
    for ($i = 1; $i -le 5; $i++) {
        $username = "user$i"
        $password = ConvertTo-SecureString "Cyberlab123!" -AsPlainText -Force
        New-ADUser -Name $username -SamAccountName $username -UserPrincipalName "$username@cyberlab.com" -AccountPassword $password -Enabled $true -Path "OU=_EMPLOYEES,DC=cyberlab,DC=com"
    }
    ```

* **Action:** Run the script.
* **Action:** Open ADUC and verify the new users are in the "_EMPLOYEES" OU.
* **Action:** Attempt to log in to "Client-1" as one of the new users (e.g., "cyberlab.com\user1").
* **Explanation:** This script creates five new users in the "_EMPLOYEES" OU. This tests remote access for a standard user.
* **Screenshot Hint:** Capture a screenshot of ADUC showing the newly created users and a screenshot of the login screen to client one with one of the new users attempting to log in.

---

## Finish Up

* Do not delete the VMs.
* "Stop" or "Deallocate" VMs to save costs.
