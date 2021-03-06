---
title: How to configure the Azure Stack Hub user's PowerShell environment
description: Configure the Azure Stack Hub user's PowerShell environment
services: azure-stack
author: Chris Black
reviewer: Daniel Brennand
lastreviewed: 27/03/2020 09:58:00

toc_rootlink: Users
toc_sub1: How To
toc_sub2: Configure Environment
toc_sub3:
toc_sub4:
toc_title: Configure the Azure Stack Hub user's PowerShell environment
toc_fullpath: Users/How To/azs-how-configure-powershell-users.md
toc_mdlink: azs-how-configure-powershell-users.md
---

# How to configure the Azure Stack Hub user's PowerShell environment

As an Azure Stack Hub user, you can use PowerShell to manage Azure Stack Hub resources such as create virtual machines, deploy Azure Resource Manager templates, etc. This topic is scoped to use with the user environments only. In order to interact with Azure Stack Hub PowerShell, you will need to set up your environment. To do so follow the below guide:

## Prerequisites

Prerequisites from a Windows-based external client.

- PowerShell 5.1

  > [!NOTE]
  > To check your version, run `$PSVersionTable.PSVersion` and compare the "Major" version.
  >
  > For "legacy" operating systems such as Windows Server 2008 R2, Windows 7, Windows Server 2012, Windows Server 2012 R2 and Windows 8.1 you will need to download the [Windows Management Framework 5.1](https://www.microsoft.com/en-us/download/details.aspx?id=54616)

## Declare variables

Enter details below to provide values for the variables in the scripts in this article:

| Variable name  | Variable description                                | Input            |
|----------------|-----------------------------------------------------|------------------|
| \$ArmEndpoint   | The Azure Resource Manager endpoint for Azure Stack Hub | <form oninput="result.value=armendpoint.value;result2.value=armendpoint.value" id="armendpoint" style="display: inline;"><input type="text" id="armendpoint" name="armendpoint" style="display: inline;" placeholder="https://management.frn00006.azure.ukcloud.com"/></form> |
| \$AzsUsername  | Your AAD username                                   | <form oninput="result.value=username.value" id="username" style="display: inline;"><input type="text" id="username" name="username" style="display: inline;" placeholder="user@contoso.onmicrosoft.com"/></form> |
| \$AzsPassword  | Your AAD password                                   | <form oninput="result.value=password.value" id="password" style="display: inline;"><input type="text" id="password" name="password" style="display: inline;" placeholder="Password123!"/></form> |

## Install Azure Stack Hub PowerShell

<pre><code class="language-PowerShell"># Set Execution Policy
Set-ExecutionPolicy RemoteSigned
  
# PowerShell commands for Azure Stack Hub are installed through the PSGallery repository
# To register the PSGallery repository, open an elevated PowerShell session and run the following command:
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
  
# Uninstall existing versions of Azure/Azure Stack Hub PowerShell
Get-Module -Name Azs.*, Azure* -ListAvailable | Uninstall-Module -Force -Verbose
  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper -Verbose

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.1 -Verbose
</code></pre>

## Configure the user environment and sign in to Azure Stack Hub

### Azure Active Directory (AAD) based deployments

<pre><code class="language-PowerShell"># Set Execution Policy
Set-ExecutionPolicy RemoteSigned

# Declare endpoint
$ArmEndpoint = "<output form="armendpoint" name="result" style="display: inline;">https://management.frn00006.azure.ukcloud.com</output>"

# Register an AzureRM environment that targets your Azure Stack Hub instance
Add-AzureRmEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

# Sign in to your environment
Connect-AzureRmAccount -EnvironmentName "AzureStackUser"
</code></pre>

### Azure Active Directory (AAD) based deployments - Embedded Credentials

<pre><code class="language-PowerShell"># Set Execution Policy
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

# Declare endpoint
$ArmEndpoint = "<output form="armendpoint" name="result2" style="display: inline;">https://management.frn00006.azure.ukcloud.com</output>"

# Register an AzureRM environment that targets your Azure Stack Hub instance
Add-AzureRmEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

# Create your Credentials
$AzsUsername = "<output form="username" name="result" style="display: inline;">user@contoso.onmicrosoft.com</output>"
$AzsPassword = '<output form="password" name="result" style="display: inline;">Password123!</output>'
$AzsUserPassword = ConvertTo-SecureString -String $AzsPassword -AsPlainText -Force
$AzsCred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AzsUsername, $AzsUserPassword

# Sign in to your environment
Connect-AzureRmAccount -Credential $AzsCred -EnvironmentName "AzureStackUser"
</code></pre>

## Test the connectivity

Now that we've got everything set-up, let's use PowerShell to create resources within Azure Stack Hub. For example, you can create a resource group for an application and add a virtual machine. Use the following command to create a resource group named "MyResourceGroup":

<pre><code class="language-PowerShell"># Get location of Azure Stack Hub
$Location = (Get-AzureRmLocation).Location

New-AzureRmResourceGroup -Name "MyResourceGroup" -Location $Location
</code></pre>

## Next steps

- [Develop templates for Azure Stack Hub](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-develop-templates)

- [Deploy templates with PowerShell](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-deploy-template-powershell)

## Feedback

If you find a problem with this article, click **Improve this Doc** to make the change yourself or raise an [issue](https://github.com/UKCloud/documentation/issues) in GitHub. If you have an idea for how we could improve any of our services, send an email to <feedback@ukcloud.com>.
