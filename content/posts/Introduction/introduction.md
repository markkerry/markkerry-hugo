---
title: "Introduction"
date: 2021-02-22T17:47:44Z
draft: false
---

## Title here

Content of the post

![WHYYY](images/wsl.jpg)

link to [Github](https://github.com/markkerry)

```powershell
$uptime = Get-ComputerInfo | Select-Object OSUptime
if ($Uptime.OsUptime.Days -ge 7) {
    Write-Output "Device has not rebooted in $($Uptime.OsUptime.Days) days, notify user to reboot"
}
else {
    Write-Output "Device has rebooted $($Uptime.OsUptime) days ago"
}
```

---

table

| Name | Age |
| ---- | --- |
| Mark | Old |
