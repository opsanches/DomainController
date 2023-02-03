# Joining workstation in the Domais using PowerShell
https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/add-computer?view=powershell-5.1

1. Make sure the computer name is correct
    if not, change is by using the PS command Rename-Computer
    https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/rename-computer?view=powershell-7.3

    Example 1: Rename the local computer
    This command renames the local computer to Server044 and then restarts it to make the change effective.
    # Rename-Computer -NewName "Server044" -DomainCredential Domain01\Admin01
    
    Example 2: Rename a remote computer
    This command renames the Srv01 computer to Server001. The computer is not restarted.
    The DomainCredential parameter specifies the credentials of a user who has permission to rename computers in the domain. The Force parameter suppresses the confirmation prompt.
    # Rename-Computer -ComputerName "Srv01" -NewName "Server001" -DomainCredential Domain01\Admin01 -Force

2. Set the DNS Server of the computer to the server's DNS 
    https://pureinfotech.com/change-dns-windows-10/
    # Get-NetIPConfiguration
    Take note on the InterfaceIndex <ID>

    # Set-DnsClientServerAddress -InterfaceIndex <ID> -ServerAddresses 10.0.2.100, 1.1.1.1

3. Join the computer to Damain

    # add-computer –domainname "YourDomainName" -credential <abc\username> -force -restart