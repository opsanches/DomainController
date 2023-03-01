# Domain Controller Clean Setup

Windows Server Core 2019

User: Administrator
Pass: M@$t3rY0d4


### Make PowerShell default console

```shell
Set-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows NT\CurrentVersion\WinLogon' -Name Shell -Value "PowerShell.exe -noExit'
```

### Change Sconfig behaviour

```shell
Set-SConfig -AutoLaunch $false
```

```shell
Set-SConfig -AutoLaunch $true
```

## Remote Access using SSH
To validate your environment, open an elevated PowerShell session and do the following:

- Type `winver.exe` and press enter to see the version details for your Windows device.
- Run `$PSVersionTable.PSVersion`. Verify your major version is at least 5, and your minor version at least 1.
- Run the command below. The output will show `True` when you're a member of the built-in Administrators group.

```
(New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
```
### Install OpenSSH With PowerShell

1. Run PowerShell as an Administrator and make sure that OpenSSH is available. Run the following cmdlet:
```shell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

2. Then, install the server or client components as needed:
```shell
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Both commands should return the following output:
```shell
Path          :
Online        : True
RestartNeeded : False
```

3. Start and configure OpenSSH Server for initial use
```shell
# Start the sshd service
Start-Service sshd

# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'

# Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

4. Connect to OpenSSH Server
```shell
ssh username@servername
```

Once connected, you get a message similar to the following output.
```shell
The authenticity of host 'servername (10.00.00.001)' can't be established.
ECDSA key fingerprint is SHA256:(<a large string>).
Are you sure you want to continue connecting (yes/no)?
```

Entering `yes` adds that server to the list of known SSH hosts on your Windows client.

At this point, you'll be prompted for your password. As a security precaution, your password won't be displayed as you type.

Once connected, you'll see the Windows command shell prompt

This step was based on [this gide](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell)

## Remote Access using PSSession

On the device you want to use to access the server, add the DomainController to the TrustedHosts list

1. Start the service WinRm 
    ```shell
    Start-Service WinRM
    ```

2. Check service status by Geting the list of TrustedHosts
    ```shell
    Get-Item Wsman:\LocalHost\Client\TrustedHosts
    ```

3. Add the DC Server to the trusted list
    ```shell
    Set-Item Wsman:\LocalHost\Client\TrustedHosts -value ComputerName.Domain.Company
    ```
Replace `ComputerName.Domain.Company` with the desired information

4. Start the PSSession
    ```shell
    NewPSSession -ComputerName Computer -Credential (Get-Credential)
    ```
Replace `Computer` with the computer you're accessing 

5. The previous command will return the PSSession ID. Use this id to start the session
    ```shell
    Enter-PSSession 1
    ```
