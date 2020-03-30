---
title: Snabbstart för Azure Cloud Shell - PowerShell
description: Lär dig hur du använder PowerShell i webbläsaren med Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273012"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Snabbstart för PowerShell i Azure Cloud Shell

Det här dokumentet beskriver hur du använder PowerShell i Cloud Shell i [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> En [Bash i Azure Cloud Shell](quickstart.md) Quickstart är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta Cloud Shell

1. Klicka på **Cloud Shell-knappen** från det övre navigeringsfältet i Azure-portalen

   ![](media/quickstart-powershell/shell-icon.png)

2. Välj PowerShell-miljön i listrutan och du kommer att finnas i Azure-enheten`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Kör PowerShell-kommandon

Kör vanliga PowerShell-kommandon i Cloud Shell, till exempel:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigera i Azure-resurser

 1. Lista alla dina `Azure` prenumerationer från enheten

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`till din önskade prenumeration

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Visa alla dina Azure-resurser under den aktuella prenumerationen

    Skriv `dir` om du vill visa flera vyer av dina Azure-resurser.

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

### <a name="allresources-view"></a>Visa AllResources

Skriv `dir` `AllResources` under katalogen för att visa dina Azure-resurser.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Utforska resursgrupper

 Du kan gå `ResourceGroups` till katalogen och i en viss resursgrupp kan du hitta virtuella datorer.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Du kanske märker att andra `dir`gången när du skriver kan Cloud Shell visa objekten mycket snabbare.
> Detta beror på att de underordnade objekten cachelagras i minnet för en bättre användarupplevelse.
Du kan dock `dir -Force` alltid använda för att få nya data.

### <a name="navigate-storage-resources"></a>Navigera i lagringsresurser

Genom att gå `StorageAccounts` in i katalogen kan du enkelt navigera i alla dina lagringsresurser

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Med anslutningssträngen kan du använda följande kommando för att montera Azure Files-resursen.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Mer information finns i [Montera en Azure-filresurs och komma åt resursen i Windows][azmount].

Du kan också navigera i katalogerna under Azure Files-resursen enligt följande:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagera med virtuella datorer

Du hittar alla dina virtuella datorer `VirtualMachines` under den aktuella prenumerationen via katalogen.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Anropa PowerShell-skript över fjärr-virtuella datorer

 > [!WARNING]
 > Se [Felsökning av fjärrhantering av virtuella Azure-datorer](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Förutsatt att du har en virtuell dator, MyVM1, låt oss använda `Invoke-AzVMCommand` för att anropa ett PowerShell-skriptblock på fjärrdatorn.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Du kan också navigera till VirtualMachines-katalogen först och köra `Invoke-AzVMCommand` enligt följande.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Logga interaktivt in på en fjärrdyv.

Du kan `Enter-AzVM` använda för att interaktivt logga in på en virtuell dator som körs i Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Du kan också `VirtualMachines` navigera till `Enter-AzVM` katalogen först och köra enligt följande

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Upptäck webbappar

Genom att gå `WebApps` in i katalogen kan du enkelt navigera i dina webbappresurser

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

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

Om du vill autentisera till servrar eller virtuella datorer med SSH genererar du `authorized_keys` det offentlig-privata `/home/user/.ssh/authorized_keys`nyckelparet i Cloud Shell och publicerar den offentliga nyckeln till på fjärrdatorn, till exempel .

> [!NOTE]
> Du kan skapa SSH privat-offentliga nycklar med hjälp av `ssh-keygen` och publicera dem i `$env:USERPROFILE\.ssh` Cloud Shell.

### <a name="using-ssh"></a>Använda SSH

Följ instruktionerna [här](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) för att skapa en ny VM-konfiguration med Azure PowerShell-cmdlets.
Innan du `New-AzVM` ringer in för att starta distributionen lägger du till SSH-offentlig nyckel i vm-konfigurationen.
Den nyligen skapade virtuella datorn innehåller `~\.ssh\authorized_keys` den offentliga nyckeln på platsen, vilket möjliggör autentiseringsuppgifter-fri SSH-session till den virtuella datorn.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Lista tillgängliga kommandon

Under `Azure` enhet `Get-AzCommand` skriver du för att hämta kontextspecifika Azure-kommandon.

Alternativt kan du alltid `Get-Command *az* -Module Az.*` använda för att ta reda på tillgängliga Azure-kommandon.

## <a name="install-custom-modules"></a>Installera anpassade moduler

Du kan `Install-Module` köra för att installera moduler från [PowerShell Gallery][gallery].

## <a name="get-help"></a>Få hjälp

Skriv `Get-Help` för att få information om PowerShell i Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

För ett visst kommando kan `Get-Help` du fortfarande göra följt av en cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Använda Azure-filer för att lagra dina data

Du kan skapa ett `helloworld.ps1`skript, säga `clouddrive` , och spara det i din att använda den över skal sessioner.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Nästa gång när du använder PowerShell `helloworld.ps1` i Cloud `$HOME\clouddrive` Shell finns filen under katalogen som monterar din Azure-filresurs.

## <a name="use-custom-profile"></a>Använd anpassad profil

Du kan anpassa din PowerShell-miljö genom att `profile.ps1` skapa `Microsoft.PowerShell_profile.ps1`PowerShell-profiler - (eller ).
Spara den `$profile.CurrentUserAllHosts` under `$profile.CurrentUserAllHosts`(eller ), så att den kan läsas in i varje PowerShell i Cloud Shell-session.

Hur du skapar en profil finns i [Om profiler][profile].

## <a name="use-git"></a>Använd Git

Om du vill klona en Git-repo i Cloud Shell måste du skapa en [personlig åtkomsttoken][githubtoken] och använda den som användarnamn. När du har din token klonar du databasen enligt följande:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Lämna gränssnittet

Skriv `exit` för att avsluta sessionen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
