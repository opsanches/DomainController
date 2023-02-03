### Steps to get Remote PSSession

0. Make PowerShell default console for the Windows Server/Workstation
''''
    Set-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon' -Name Shell -Value 'PowerShell.exe -noExit'
    '
# Add the server in the TrustedHosts list
1. Start the service WinRm
''''
    Start-Service WinRM
    '
2. Verify the service status by Geting the list of TrustedHosts
''''
    Get-Item Wsman:\LocalHost\Client\TrustedHosts
    '
3. Add the computer to the list
''''
    Set-Item Wsman:\LocalHost\Client\TrustedHosts -value <ComputerName>
    '
# Start the PSSession

''''
    NewPSSession -ComputerName <computer> -Credential (Get-Credential)

    Enter-PSSession <id>
    '
