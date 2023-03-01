# Windows-PowerShell-Get-all-workstation-names-inside-domain-and-current-logged-in-user-of-each

```
# Fix array output formatting
$pshost = get-host
$pswindow = $pshost.ui.rawui
$newsize = $pswindow.buffersize
$newsize.height = 3000
$newsize.width = 3000
$pswindow.buffersize = $newsize


# snippet will get you all your non-server PC names, sorted, and the currently logged-on user on each:
$PCList = (Get-ADComputer -Filter {OperatingSystem -notlike "*SERVER*" -and Enabled -EQ $True}).Name
[Array]::Sort($PCList)
ForEach ($PC in $PCList){
	Try{$User = (Get-WMIObject Win32_ComputerSystem -ComputerName $PC -ErrorAction Stop).UserName.split('\')[-1]}
	Catch {$User = "Problem with $($PC)."; $Failures += "$($PC)`n"; Continue}

	[PSCustomObject] @{
		"Computer" = $PC

		"Logged-in User" = $User
		}  # End PSCustomObject
}
Write-Warning "These ones failed:`n"
$Failures
```
