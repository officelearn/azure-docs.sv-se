---
title: Hur du skapar Windows Azure VM-avbildningar med förpackaren | Microsoft Docs
description: Lär dig hur du använder förpackaren för att skapa avbildningar av virtuella Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: iainfou
ms.openlocfilehash: b7f07ec8736086483f91746512f10118ee90762d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333173"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Hur du använder förpackaren för att skapa virtuella Windows-avbildningar i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Windows-distributionen och OS-version. Avbildningar kan innehålla förinstallerade program och konfigurationer. Azure Marketplace innehåller många första och tredje parts avbildningar för de vanligaste operativsystem och miljöer eller skapa egna anpassade avbildningar som är anpassade efter era behov. Den här artikeln beskriver hur du använder verktyget öppen källkod [förpackaren](https://www.packer.io/) att definiera och skapa anpassade avbildningar i Azure.


## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
När du skapar skapar förpackaren tillfälliga Azure-resurser som den skapar den Virtuella källdatorn. För att avbilda den Virtuella källdatorn som ska användas som en avbildning måste du definiera en resursgrupp. Utdata från processen för att bygga förpackaren lagras i den här resursgruppen.

Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Skapa autentiseringsuppgifter för Azure
Packare autentiserar med Azure med hjälp av ett huvudnamn för tjänsten. En Azure-tjänstens huvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och verktyg för automatisering som förpackaren. Du styr och definiera behörigheter för vilka åtgärder som tjänstens huvudnamn kan utföra i Azure.

Skapa en tjänstens huvudnamn med [ny AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) och tilldela behörigheter att skapa och hantera resurser med tjänstens huvudnamn [ny AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

För att autentisera till Azure måste du också behöva hämta din Azure-klient och prenumerations-ID med [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

Du kan använda dessa två ID: N i nästa steg.


## <a name="define-packer-template"></a>Definiera förpackaren mall
För att skapa avbildningar, skapa en mall som en JSON-fil. I mallen definierar du builders och provisioners som utför faktiska skapar. Förpackaren har en [builder för Azure](https://www.packer.io/docs/builders/azure.html) som kan du definiera Azure-resurser, t.ex. de huvudsakliga Tjänstereferenser som skapade i föregående steg.

Skapa en fil med namnet *windows.json* och klistra in följande innehåll. Ange egna värden för följande:

| Parameter                           | Var kan hämtas |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Visa service ägar-ID med `$sp.applicationId` |
| *client_secret*                     | Lösenordet du angav i `$securePassword` |
| *tenant_id*                         | Utdata från `$sub.TenantId` kommando |
| *PRENUMERATIONSID*                   | Utdata från `$sub.SubscriptionId` kommando |
| *object_id*                         | Visa service principal objekt-ID med `$sp.Id` |
| *managed_image_resource_group_name* | Namnet på resursgruppen som du skapade i det första steget |
| *managed_image_name*                | Namn för den hanterade diskavbildning som har skapats |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Den här mallen skapar du en Windows Server 2016 VM, installerar IIS och generalisering den virtuella datorn med Sysprep. Installera IIS visar hur du kan använda PowerShell provisioner för att köra ytterligare kommandon. Den slutliga förpackare bilden innehåller programvara som krävs installation och konfiguration.


## <a name="build-packer-image"></a>Skapa förpackaren bild
Om du inte redan har förpackaren installerad på din lokala dator [Följ installationsanvisningarna förpackaren](https://www.packer.io/docs/install/index.html).

Skapa avbildningen genom att ange din förpackaren mallfilen på följande sätt:

```bash
./packer build windows.json
```

Ett exempel på utdata från föregående kommandona är följande:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Det tar några minuter för förpackaren att bygga den virtuella datorn körs provisioners och rensa distributionen.


## <a name="create-a-vm-from-the-packer-image"></a>Skapa en virtuell dator från förpackaren avbildningen
Nu kan du skapa en virtuell dator från avbildningen med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Stödjande nätverksresurser skapas om de inte redan finns. När du uppmanas, anger du ett administrativa användarnamn och lösenord som ska skapas på den virtuella datorn. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage*:

```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än förpackaren avbildningen ange avbildnings-ID i stället för namn. Du kan hämta avbildnings-ID med [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage).

Det tar några minuter att skapa den virtuella datorn från förpackaren avbildningen.


## <a name="test-vm-and-webserver"></a>Testa virtuell dator och webbserver
Hämta den offentliga IP-adressen för den virtuella datorn med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Ange den offentliga IP-adressen i till en webbläsare om du vill se den virtuella datorn, som innehåller IIS-installationen från förpackaren-provisioner i åtgärden.

![Standardwebbplatsen i IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Nästa steg
I det här exemplet används förpackaren för att skapa en VM-avbildning med IIS är redan installerad. Du kan använda den här Virtuella datorn tillsammans med befintliga arbetsflöden för distribution, som att distribuera din app till virtuella datorer skapas från avbildningen med Team Services, Ansible, Chef eller Puppet.

Ytterligare exempel förpackaren mallar för andra Windows-distributioner, se [GitHub-repo](https://github.com/hashicorp/packer/tree/master/examples/azure).
