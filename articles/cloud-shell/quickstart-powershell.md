---
title: "PowerShell i Snabbstart i Azure-molnet Shell (förhandsversion) | Microsoft Docs"
description: "Snabbstart för PowerShell i molnet Shell"
services: Azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: b454720dd5bd2df036a400c8bfc1c383de5af542
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Snabbstart för PowerShell i Azure-molnet Shell (förhandsgranskning)

Det här dokumentet beskriver hur du använder PowerShell i molnet Shell i den [Azure-portalen](https://aka.ms/PSCloudPreview).

> [!NOTE]
> En [Bash i Azure Cloud Shell](quickstart.md) Quickstart är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta molnet Shell

1. Klicka på **moln Shell** knappen från övre navigeringsfältet i Azure-portalen

  ![](media/quickstart-powershell/shell-icon.png)

2. Välj i listrutan PowerShell-miljö och du kan i Azure-enheten`(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Kör PowerShell-kommandon

Kör vanliga PowerShell-kommandon i molnet-gränssnittet som:

```Powershell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigera Azure-resurser

 1. Visa en lista över dina prenumerationer

    ``` Powershell
    PS Azure:\> dir
    ```

 2. `cd`prenumerationen prioriterade

    ``` Powershell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Visa alla dina Azure-resurser under den aktuella prenumerationen
 
    Typen `dir` att lista flera vyer i Azure-resurser.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>AllResources vy 
Typen `dir` under `AllResources` directory för att visa dina Azure-resurser.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Utforska resursgrupper

 Du kan gå till den `ResourceGroups` katalog och inom en viss resursgrupp hittar virtuella datorer.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> Du kan märka som den andra gången när du skriver `dir`, moln-gränssnittet är att visa objekt som är mycket snabbare.
> Det beror på att de underordnade objekten cachelagras i minnet för en bättre användarupplevelse.
Du kan alltid använda `dir -Force` att uppdatera data.

### <a name="navigate-storage-resources"></a>Navigera lagringsresurser
    
Genom att ange i den `StorageAccounts` mappen som du kan enkelt navigera dina lagringsresurser
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

Du kan använda följande kommando för att montera resursen Azure-filer med anslutningssträngen.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

Mer information finns i [montera en resurs i Azure-filer och få åtkomst till resursen i Windows][azmount].

Du kan också navigera kataloger under Azure Files-resursen på följande sätt:

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Interagera med virtuella datorer

Du kan söka efter alla virtuella datorer under den aktuella prenumerationen via `VirtualMachines` directory.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Starta PowerShell-skript över remote virtuella datorer

 > [!WARNING]
 > Se [felsökning fjärrhantering av virtuella datorer i Azure](troubleshooting.md#powershell-resolutions).

  Anta att du har en virtuell dator, MyVM1, använder vi `Invoke-AzureRmVMCommand` att anropa en PowerShell-scriptblock på fjärrdatorn.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  Du kan också gå till katalogen för virtualMachines först och köra `Invoke-AzureRmVMCommand` på följande sätt.

  ``` Powershell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  Du kan se utdata som liknar följande:

  ``` Powershell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Logga in interaktivt till en fjärransluten VM

Du kan använda `Enter-AzureRmVM` att interaktivt logga in på en virtuell dator som körs i Azure.

  ``` Powershell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Du kan också navigera till den `virtualMachines` directory första och kör `Enter-AzureRmVM` enligt följande

  ``` Powershell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Identifiera Webbappar

Genom att ange i den `WebApps` mappen som du kan enkelt navigera resurserna web apps

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="ssh"></a>SSH

[Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) är tillgänglig i PowerShell CloudShell.
Om du vill autentisera till servrar eller virtuella datorer med SSH, generera privat-offentligt nyckelpar i CloudShell och publicera den offentliga nyckeln till `authorized_keys` på fjärrdatorn, t.ex `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Du kan skapa SSH privat-offentliga nycklar med hjälp av `ssh-keygen` och publicera dem till `$env:USERPROFILE\.ssh` i CloudShell.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>Med hjälp av en anpassad profil för att bevara GIT och SSH-inställningar

Eftersom sessioner inte sparas vid utloggning spara din `$env:USERPROFILE\.ssh` mappen `CloudDrive` eller skapa en symlink CloudShell hämtar startades.
Lägg till följande kod liten i din profile.ps1 för att skapa en symlink till CloudDrive.

``` Powershell
# Check if the ssh folder exists
if( -not (Test-Path $home\CloudDrive\.ssh){
    mkdir $home\CloudDrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>Med hjälp av SSH

Följ anvisningarna [här](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) att skapa en ny VM-konfiguration med hjälp av AzureRM Cmdlets.
Innan anrop till `New-AzureRMVM` för att kick-off distributionen, lägga till offentlig SSH-nyckel i VM-konfiguration.
Den nyligen skapade virtuella datorn innehåller den offentliga nyckeln i den `~\.ssh\authorized_keys` plats, så att autentiseringsuppgifter utan ssh-session till den virtuella datorn.

``` Powershell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH Keys in CloudShell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# ssh to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>Lista över tillgängliga kommandon

Under `Azure` enhet, Skriv `Get-AzureRmCommand` få sammanhangsberoende Azure kommandon.

Du kan alltid använda `Get-Command *azurerm* -Module AzureRM.*` ta reda på de tillgängliga Azure kommandona.

## <a name="install-custom-modules"></a>Installera anpassade moduler

Du kan köra `Install-Module` installera moduler från den [PowerShell-galleriet][gallery].

## <a name="get-help"></a>Get-Help

Typen `Get-Help` att hämta information om PowerShell i Azure Cloud-gränssnittet.

``` Powershell
PS Azure:\> Get-Help
```

Du kan fortfarande göra följt av en cmdlet Get-Help för ett specifikt kommando.

``` Powershell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Använda Azure-filer för att lagra dina data

Du kan skapa ett skript, säg `helloworld.ps1`, och spara den till din `CloudDrive` att använda över shell-sessioner.

``` Powershell
cd C:\users\ContainerAdministrator\CloudDrive
PS C:\users\ContainerAdministrator\CloudDrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\CloudDrive> .\helloworld.ps1
Hello World!
```

Nästa gång när du använder PowerShell molnet Shell, den `helloworld.ps1` filen kommer att finnas den `CloudDrive` mapp som monterar filer för Azure-resurs.

## <a name="use-custom-profile"></a>Använd anpassad profil

Du kan anpassa din PowerShell-miljö genom att skapa PowerShell eller profilerna - `profile.ps1` eller `Microsoft.PowerShell_profile.ps1`. Spara den under den `CloudDrive` så att den kan läsas in i varje PowerShell-session när du startar gränssnittet molnet.

Hur du skapar en profil avser [om profiler][profile].

## <a name="use-git"></a>Använda Git

Om du vill klona en git-lagringsplatsen i molnet-gränssnittet, måste du skapa en [personlig åtkomsttoken] [ githubtoken] och använda den som användarnamnet. När du har skapat din token, klona databasen på följande sätt:

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Eftersom sessioner i molnet Shell inte sparas när du loggar ut eller sessionen gör timeout, finns inte i konfigurationsfilen Git vid nästa inloggning. Om du vill bevara Git config, måste du spara dina .gitconfig till din `CloudDrive` och kopiera den eller skapa en symlink molnet Shell hämtar startades. Använd följande kodavsnitt i din profile.ps1 för att skapa en symlink till `CloudDrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $Home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Lämna gränssnittet

Typen `exit` att avsluta sessionen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
