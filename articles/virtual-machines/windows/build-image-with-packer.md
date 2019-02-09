---
title: Hur du skapar Windows VM-avbildningar med Packer i Azure | Microsoft Docs
description: Lär dig hur du använder Packer för att skapa avbildningar av Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: cynthn
ms.openlocfilehash: 0ae4c883baa156276646755273547a17d23edc55
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982496"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Hur du använder Packer för att skapa Windows-avbildningar i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar den Windows-distribution och operativsystemsversion. Bilder kan innehålla förinstallerade program och konfigurationer. Azure Marketplace erbjuder många avbildningar av första och tredje part för de vanligaste OS och programmiljöer, eller skapa dina egna anpassade avbildningar som är specialanpassade utifrån dina behov. Den här artikeln beskriver hur du använder Verktyg för öppen källkod [Packer](https://www.packer.io/) att definiera och skapa anpassade avbildningar i Azure.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
När du skapar skapar Packer tillfällig Azure-resurser som den bygger den Virtuella källdatorn. För att avbilda den Virtuella källdatorn för användning som en avbildning måste du definiera en resursgrupp. Utdata från skapandeprocessen Packer lagras i den här resursgruppen.

Skapa en resursgrupp med [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiserar med Azure med ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Packer. Du kontrollerar och definiera behörigheter om vilka åtgärder som tjänstens huvudnamn kan utföra i Azure.

Skapa ett tjänstobjekt med [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) och tilldela behörigheter för tjänstens huvudnamn att skapa och hantera resurser med [New AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Ersätt *&lt;lösenord&gt;* i exempel med ditt eget lösenord.  

```powershell
$sp = New-AzADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "<password>" -AsPlainText -Force)
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Om du vill autentisera till Azure måste du också behöva hämta dina Azure-klient och prenumeration ID: N med [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
$sub = Get-AzSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

Du kan använda dessa två ID: N i nästa steg.


## <a name="define-packer-template"></a>Definiera Packer-mall
Avbildningar skapa måste du en mall som en JSON-fil. I mallen definierar du builders och provisioners som utför den faktiska skapandeprocessen. Packer har en [builder för Azure](https://www.packer.io/docs/builders/azure.html) som gör det möjligt att definiera Azure-resurser, t.ex. autentiseringsuppgifterna för tjänstobjektet skapade i föregående steg.

Skapa en fil med namnet *windows.json* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Var de kan hämtas |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Visa ID för tjänstens huvudnamn med `$sp.applicationId` |
| *client_secret*                     | Lösenordet du angav i `$securePassword` |
| *tenant_id*                         | Utdata från `$sub.TenantId` kommando |
| *subscription_id*                   | Utdata från `$sub.SubscriptionId` kommando |
| *managed_image_resource_group_name* | Namnet på resursgruppen som du skapade i det första steget |
| *managed_image_name*                | Namn för den hantera diskavbildningen som har skapats |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

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

Den här mallen skapar en virtuell Windows Server 2016-dator, installerar IIS och sedan generaliserar den virtuella datorn med Sysprep. Installationen av IIS visar hur du kan använda PowerShell-provisioner köra ytterligare kommandon. Den slutliga Packer-avbildningen innehåller nödvändig programvara installation och konfiguration.


## <a name="build-packer-image"></a>Skapa Packer-avbildning
Om du inte redan har Packer som installerats på den lokala datorn [Följ installationsanvisningarna Packer](https://www.packer.io/docs/install/index.html).

Skapa avbildningen genom att ange din Packer mallfilen på följande sätt:

```bash
./packer build windows.json
```

Ett exempel på utdata från föregående kommandon är följande:

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

Det tar några minuter för Packer att skapa den virtuella datorn, kör provisioners och rensa distributionen.


## <a name="create-a-vm-from-the-packer-image"></a>Skapa en virtuell dator från Packer-avbildning
Du kan nu skapa en virtuell dator från avbildningen med [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Stödnätverksresurser skapas om de inte redan finns. När du uppmanas, anger du ett administratörsanvändarnamn och lösenord som ska skapas på den virtuella datorn. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage*:

```powershell
New-AzVm `
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

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än din Packer-avbildning, ange avbildnings-ID i stället för namnet på avbildningen. Du kan hämta avbildnings-ID med [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Det tar några minuter att skapa den virtuella datorn från en Packer-avbildning.


## <a name="test-vm-and-webserver"></a>Testa virtuell dator och webbserver
Hämta den offentliga IP-adressen för den virtuella datorn med [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Ange den offentliga IP-adressen i en webbläsare om du vill se den virtuella datorn, som innefattar installationen av IIS från Packer-provisioner i praktiken.

![Standardwebbplatsen i IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Nästa steg
I det här exemplet används Packer för att skapa en VM-avbildning med IIS är redan installerad. Du kan använda den här Virtuella datorn tillsammans med befintliga arbetsflöden för distribution, till exempel att distribuera din app till virtuella datorer som skapats från avbildningen med Azure DevOps-tjänsterna, Ansible, Chef eller Puppet.

Ytterligare exempel Packer på mallar för andra Windows-distributioner, se [denna GitHub-lagringsplats](https://github.com/hashicorp/packer/tree/master/examples/azure).
