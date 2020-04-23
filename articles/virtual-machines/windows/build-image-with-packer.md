---
title: Så här skapar du Windows VM-avbildningar med Packer
description: Lär dig hur du använder Packer för att skapa avbildningar av virtuella Windows-datorer i Azure
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: f813551ed665628898bb219a611947c3026ac67c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084489"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Så här använder du Packer för att skapa windows-avbildningar för virtuella datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Windows-distributions- och OS-versionen. Avbildningar kan innehålla förinstallerade program och konfigurationer. Azure Marketplace innehåller många avbildningar från första och tredje part för de vanligaste operativsystem- och programmiljöerna, eller så kan du skapa egna anpassade avbildningar som är anpassade efter dina behov. I den här artikeln beskrivs hur du använder verktyget med öppen källkod för att definiera och skapa anpassade [avbildningar](https://www.packer.io/) i Azure.

Den här artikeln testades senast den 2019-02-21 med [Az PowerShell-modulversionen](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.3.0 och [Packer](https://www.packer.io/docs/install/index.html) version 1.3.4.

> [!NOTE]
> Azure har nu en tjänst, Azure Image Builder (förhandsversion), för att definiera och skapa egna anpassade avbildningar. Azure Image Builder bygger på Packer, så du kan även använda dina befintliga Packer shell provisioner skript med den. Information om hur du kommer igång med Azure Image Builder finns i [Skapa en Windows-virtuell dator med Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
Under byggprocessen skapar Packer tillfälliga Azure-resurser när den bygger källdatorn. Om du vill fånga den virtuella källdatorn för att användas som avbildning måste du definiera en resursgrupp. Utdata från Packer-byggprocessen lagras i den här resursgruppen.

Skapa en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiserar med Azure med hjälp av ett tjänsthuvudnamn. Ett Azure-tjänsthuvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Packer. Du styr och definierar behörigheterna för vilka åtgärder tjänstens huvudnamn kan utföra i Azure.

Skapa ett tjänsthuvudnamn med [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) och tilldela behörigheter för tjänstens huvudnamn att skapa och hantera resurser med [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Värdet för `-DisplayName` måste vara unikt; ersätta med ditt eget värde efter behov.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Sedan mata ut lösenordet och ansökan ID.

```powershell
$plainPassword
$sp.ApplicationId
```


För att autentisera till Azure måste du också skaffa dina Azure-klient- och prenumerations-ID:er med [Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definiera packermall
Om du vill skapa bilder skapar du en mall som en JSON-fil. I mallen definierar du byggare och etablerare som utför den faktiska byggprocessen. Packer har en [byggare för Azure](https://www.packer.io/docs/builders/azure.html) som låter dig definiera Azure-resurser, till exempel tjänstens huvudreferenser som skapats i föregående steg.

Skapa en fil med namnet *windows.json* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Om att få |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Visa tjänstens huvud-ID med`$sp.applicationId` |
| *client_secret*                     | Visa det automatiskt genererade lösenordet med`$plainPassword` |
| *tenant_id*                         | Utdata `$sub.TenantId` från kommandot |
| *subscription_id*                   | Utdata `$sub.SubscriptionId` från kommandot |
| *managed_image_resource_group_name* | Namn på resursgrupp som du skapade i det första steget |
| *managed_image_name*                | Namn på den hanterade diskavbildningen som skapas |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
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

Den här mallen skapar en virtuell dator med Windows Server 2016, installerar IIS och generaliserar sedan den virtuella datorn med Sysprep. IIS-installationen visar hur du kan använda PowerShell-etableringsverktyget för att köra ytterligare kommandon. Den slutliga Packer-avbildningen innehåller sedan den nödvändiga programinstallationen och konfigurationen.


## <a name="build-packer-image"></a>Skapa Packer-avbildning
Om Packer inte redan har installerat Packer på den lokala datorn [följer du installationsinstruktionerna för Packer](https://www.packer.io/docs/install/index.html).

Skapa bilden genom att öppna en cmd-prompt och ange mallfilen Packer enligt följande:

```
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

Det tar några minuter för Packer att skapa den virtuella datorn, köra etableringselementen och rensa distributionen.


## <a name="create-a-vm-from-the-packer-image"></a>Skapa en virtuell dator från Packer-avbildningen
Du kan nu skapa en virtuell dator från avbildningen med [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). De stödjande nätverksresurserna skapas om de inte redan finns. Ange ett administrativt användarnamn och lösenord som ska skapas på den virtuella datorn när du uppmanas att göra det. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage:*

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

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än Packer-avbildningen anger du bild-ID:t i stället för bildnamnet. Du kan hämta bild-ID med [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Det tar några minuter att skapa den virtuella datorn från Packer-avbildningen.


## <a name="test-vm-and-webserver"></a>Testa VM och webbserver
Hämta den offentliga IP-adressen för den virtuella datorn med [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Om du vill se din virtuella dator, som inkluderar IIS-installationen från Packer-etableringsadressen, anger du i praktiken den offentliga IP-adressen i en webbläsare.

![Standardwebbplatsen i IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga Packer-etableringsskript med [Azure Image Builder](image-builder.md).
