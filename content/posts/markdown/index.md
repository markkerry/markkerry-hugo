---
title: "Markdown Reference"
date: 2021-02-22T17:47:44Z
draft: false
---

A quick reference for writing in Markdown.

## Headers

```markdown
# H1
## H2
### H3
etc
```
Display as follows:

# H1

## H2

### H3

---

## Code Snips

Add a code snip as follows

<pre>
```powershell
$uptime = Get-ComputerInfo | Select-Object OSUptime
if ($Uptime.OsUptime.Days -ge 7) {
    Write-Output "Device has not rebooted in $($Uptime.OsUptime.Days) days"
    Write-Output "Non-Compliant"
    exit 1
}
else {
    Write-Output "Device has rebooted $($Uptime.OsUptime) days ago"
    Write-Output "Compliant"
    exit 0
}
```
</pre>

And the following will display:

```PowerShell
$uptime = Get-ComputerInfo | Select-Object OSUptime
if ($Uptime.OsUptime.Days -ge 7) {
    Write-Output "Device has not rebooted in $($Uptime.OsUptime.Days) days"
    Write-Output "Non-Compliant"
    exit 1
}
else {
    Write-Output "Device has rebooted $($Uptime.OsUptime) days ago"
    Write-Output "Compliant"
    exit 0
}
```

---

## Bold and Italics

Bold __text__ can be defined in **two** different ways. Using double underscores `'__'` or double asterisk `'**'`

<pre>
Bold __text__ can be defined in **two** different ways
</pre>

Italic _text_ can be defined in *two* different ways. Using double underscores `'_'` or double asterisk `'*'`

<pre>
Italic _text_ can be defined in *two* different ways
</pre>

---

## Lists

Ordered lists as follows

<pre>
1. Item 1
1. Item 2
  1. Item 2a
  1. Item 2b
</pre>

1. Item 1
1. Item 2
  1. Item 2a
  1. Item 2b

Unordered lists

<pre>
* Item 1
* Item 2
  * Item 2a
  * Item 2b
</pre>

* Item 1
* Item 2
  * Item 2a
  * Item 2b

  ---

![WHYY](images/wsl.jpg)

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
