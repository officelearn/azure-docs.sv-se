---
title: Skapa en virtuell dator i DevTest Labs med Azure PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för att skapa och hantera virtuella datorer med Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622447"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Skapa en virtuell dator med DevTest Labs med Azure PowerShell
Den här artikeln visar hur du skapar en virtuell dator i Azure DevTest Labs med hjälp av Azure PowerShell. Du kan använda PowerShell-skript för att automatisera skapandet av virtuella datorer i ett labb i Azure DevTest Labs. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar:

- [Skapa ett labb](devtest-lab-create-lab.md) om du inte vill använda en befintlig labb för att testa det skript eller kommandon i den här artikeln. 
- [Installera Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) eller använda Azure Cloud Shell som är integrerat i Azure-portalen. 

## <a name="powershell-script"></a>PowerShell-skript
Exempelskriptet i det här avsnittet använder de [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet.  Den här cmdleten tar den testmiljön resurs-ID, namnet på åtgärden som ska utföras (`createEnvironment`), och parametrarna som behövs för att utföra åtgärden. Parametrarna är i en hashtabell som innehåller alla egenskaper för VM-beskrivning. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Egenskaperna för den virtuella datorn i skriptet ovan kan vi skapa en virtuell dator med Windows Server 2016 DataCenter som Operativsystemet. De här egenskaperna kan variera för varje typ av virtuell dator. Den [definiera VM](#define-virtual-machine) avsnittet visar hur du avgör vilka egenskaper du vill använda i det här skriptet.

Följande kommando innehåller ett exempel för att köra skriptet som sparats i ett filnamn: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definiera virtuella datorn
Det här avsnittet visar hur du hämtar de egenskaper som är specifika för en typ av virtuell dator som du vill skapa. 

### <a name="use-azure-portal"></a>Använda Azure-portalen
Du kan skapa en Azure Resource Manager-mall när du skapar en virtuell dator i Azure-portalen. Du behöver inte slutföra processen att skapa den virtuella datorn. Du kan bara följa stegen tills du ser mallen. Det här är det bästa sättet att få nödvändiga JSON-beskrivning om du inte redan har ett labb som virtuell dator som skapats. 

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** på menyn till vänster navigering.
3. Sök efter och välj **DevTest Labs** från listan över tjänster. 
4. På den **DevTest Labs** väljer du ditt labb i listan över labs.
5. På startsidan för labbet, väljer **+ Lägg till** i verktygsfältet. 
6. Välj en **basavbildningen** för den virtuella datorn. 
7. Välj **automatiseringsalternativ** längst ned på sidan ovan den **skicka** knappen. 
8. Du ser den **Azure Resource Manager-mall** för att skapa den virtuella datorn. 
9. JSON-segmentet i den **resurser** -avsnittet innehåller definitionen för vilken typ av avbildning du valde tidigare. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

I det här exemplet visas hur du hämtar en definition av en Azure Marketplace-avbildning. Du kan få en definition av en anpassad avbildning, en formel eller en miljö på samma sätt. Lägg till alla artefakter som krävs för den virtuella datorn och ange avancerade inställningar som krävs. När du har angett värden för fälten som krävs och eventuella valfria fält innan att välja den **automatiseringsalternativ** knappen.

### <a name="use-azure-rest-api"></a>Använd Azure REST API
Följande procedur ger dig steg för att hämta egenskaper för en avbildning med hjälp av REST-API: De här stegen fungerar endast för en befintlig virtuell dator i ett labb. 

1. Navigera till den [Virtual Machines – lista](/rest/api/dtl/virtualmachines/list) väljer **prova** knappen. 
2. Välj din **Azure-prenumeration**.
3. Ange den **resursgrupp för labbet**.
4. Ange den **namnet labbet**. 
5. Välj **Kör**.
6. Du ser den **egenskaper för avbildningen** baserat på den virtuella datorn har skapats. 



## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure PowerShell-dokumentationen för Azure DevTest Labs](/powershell/module/az.devtestlabs/)
