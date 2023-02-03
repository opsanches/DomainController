### Creating and setting up Active Directory

# https://xpertstec.com/how-to-install-active-directory-windows-server-core-2022/ #

PREP

# Use SCONFIG to:
1. Set a hostname to the windows server
2. Assign it an static IP Address
''''10.0.2.100/24 10.0.2.2
3. Change the DNS Server to the server's own IP Address

# Install Active Directory
1. Use 'Get-WindowsFeature' tool to locate the AD Domain Services
''''
    Get-WindowsFeature | ? {$_.Name -LIKE "ad*"}
    '
2. Install AD-Domain-Tools using 'install-windowsfeature' command
''''
    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    '

# Setup AD on the server
1. Import the module Active Directory A S Deployment and install
''''
    Import-Module ADDSDeployment
    Install-ADDSForest
    '
2. Give the domain and assign it a Password.
'''''
    Wait till the installation is done
    '
3. By default, the DNS Address is set to a loop Address. Change it back to the server address using SCONFIG