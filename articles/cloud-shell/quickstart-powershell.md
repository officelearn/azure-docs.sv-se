---
title: PowerShell i Azure Cloud Shell Snabbstart | Microsoft Docs
description: Snabbstart för PowerShell i Cloudshell
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: damaerte
ms.openlocfilehash: 0bce9f50bdc3ac8fb4675a7ac2a3fb300036973f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404367"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Snabbstart för PowerShell i Azure Cloudshell

Det här dokumentet beskriver hur du använder PowerShell i Cloud Shell i den [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> En [Bash i Azure Cloud Shell](quickstart.md) Snabbstart finns också.

## <a name="start-cloud-shell"></a>Starta Cloudshell

1. Klicka på **Cloud Shell** knappen från det övre navigeringsfältet i Azure Portal

  ![](media/quickstart-powershell/shell-icon.png)

2. Välj PowerShell-miljö från listrutan och du kan i Azure-enheten `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Kör PowerShell-kommandon

Kör till exempel vanliga PowerShell-kommandon i Cloud Shell:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzureRmVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigera Azure-resurser

 1. Lista över alla prenumerationer från `Azure` enhet

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` till din önskade prenumeration

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Visa alla dina Azure-resurser under den aktuella prenumerationen

    Typ `dir` visa en lista över flera vyer i dina Azure-resurser.

    ```azurepowershell-interactive
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

Typ `dir` under `AllResources` directory för att visa dina Azure-resurser.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Utforska resursgrupper

 Du kan gå till den `ResourceGroups` katalogen och i en specifik resursgrupp du hittar virtuella datorer.

```azureowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Du kanske ser som den andra gången när du skriver `dir`, Cloud Shell kan visa objekt som är mycket snabbare.
> Det beror på att de underordnade objekten cachelagras i minnet för en bättre användarupplevelse.
Men du kan alltid använda `dir -Force` att uppdatera data.

### <a name="navigate-storage-resources"></a>Navigera lagringsresurser

Genom att ange i den `StorageAccounts` katalogen, du kan enkelt navigera dina lagringsresurser

```azureowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Med anslutningssträngen kan du använda följande kommando för att montera Azure Files-resurs.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Mer information finns i [montera en Azure Files-resurs och få åtkomst till resursen i Windows][azmount].

Du kan också navigera till kataloger under Azure Files-resurs på följande sätt:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagera med virtuella datorer

Du kan hitta alla virtuella datorer under den aktuella prenumerationen via `VirtualMachines` directory.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Anropa PowerShell-skript för fjärranslutna virtuella datorer

 > [!WARNING]
 > Se [felsökning fjärrhantering av virtuella Azure-datorer](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Om vi antar att du har en virtuell dator, MyVM1, använder vi `Invoke-AzVMCommand` att anropa ett PowerShell-skriptblock på fjärrdatorn.

  ```azurepowershell-interactive
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Du kan också gå till katalogen för virtuella datorer först och köra `Invoke-AzVMCommand` på följande sätt.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo}

  # You will see output similar to the following:

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Logga in interaktivt på en fjärransluten virtuell dator

Du kan använda `Enter-AzVM` att interaktivt logga in på en virtuell dator som körs i Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Du kan också navigera till den `VirtualMachines` directory första och kör `Enter-AzVM` på följande sätt

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM
 ```

### <a name="discover-webapps"></a>Identifiera Webbappar

Genom att ange i den `WebApps` katalogen, du kan enkelt navigera dina webbresurser för appar

```azurepowershell-interactive
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

För att autentisera till servrar eller virtuella datorer med SSH, generera offentligt / privat nyckelpar i Cloud Shell och publicera den offentliga nyckeln till `authorized_keys` på fjärrdatorn, till exempel `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Du kan skapa SSH-privata offentliga nycklar med `ssh-keygen` och publicerar dem `$env:USERPROFILE\.ssh` i Cloud Shell.

### <a name="using-ssh"></a>Med hjälp av SSH

Följ instruktionerna [här](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) att skapa en ny VM-konfiguration med hjälp av AzureRM-cmdletar.
Tidigare anrop till `New-AzureRmVM` för att sätta igång distributionen, lägga till offentlig SSH-nyckel i VM-konfigurationen.
Den nya virtuella datorn innehåller den offentliga nyckeln i den `~\.ssh\authorized_keys` plats, vilket innebär att autentiseringsuppgifter är kostnadsfria SSH-session till den virtuella datorn.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Lista tillgängliga kommandon

Under `Azure` enhet, Skriv `Get-AzureRmCommand` att hämta sammanhangsberoende Azure-kommandon.

Du kan alltid använda `Get-Command *azurerm* -Module AzureRM.*` att ta reda på tillgängliga Azure-kommandon.

## <a name="install-custom-modules"></a>Installera anpassade moduler

Du kan köra `Install-Module` installera moduler från den [PowerShell-galleriet][gallery].

## <a name="get-help"></a>Get-Help

Typ `Get-Help` att hämta information om PowerShell i Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

För ett visst kommando kan du fortfarande göra `Get-Help` följt av en cmdlet.

```azurepowershell-interactive
Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Använda Azure Files för att lagra dina data

Du kan skapa ett skript, exempelvis `helloworld.ps1`, och spara den i din `clouddrive` kan använda den shell-sessioner.

```azurepowershell-interactive
cd $HOME\clouddrive
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Nästa gång när du använder PowerShell i Cloud Shell den `helloworld.ps1` filen kommer att finnas den `$HOME\clouddrive` katalog som monterar Azure Files-resurs.

## <a name="use-custom-profile"></a>Använd anpassad profil

Du kan anpassa din PowerShell-miljö genom att skapa PowerShell profil(er) - `profile.ps1` (eller `Microsoft.PowerShell_profile.ps1`).
Spara den under `$profile.CurrentUserAllHosts` (eller `$profile.CurrentUserAllHosts`), så att den kan läsas in i varje PowerShell i Cloud Shell-sessionen.

Hur du skapar en profil avser [om profiler][profile].

## <a name="use-git"></a>Använd Git

Om du vill klona en Git-lagringsplats i Cloud Shell måste du skapa en [personlig åtkomsttoken] [ githubtoken] och använda den som användarnamnet. När du har din token, klona databasen på följande sätt:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Lämna gränssnittet

Typ `exit` att avsluta sessionen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
