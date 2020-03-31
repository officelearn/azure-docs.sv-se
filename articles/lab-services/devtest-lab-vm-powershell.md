---
title: Skapa en virtuell dator i DevTest Labs med Azure PowerShell
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167114"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Skapa en virtuell dator med DevTest Labs med Azure PowerShell
Den här artikeln visar hur du skapar en virtuell dator i Azure DevTest Labs med hjälp av Azure PowerShell. Du kan använda PowerShell-skript för att automatisera skapandet av virtuella datorer i ett labb i Azure DevTest Labs. 

## <a name="prerequisites"></a>Krav
Innan du börjar:

- [Skapa ett labb](devtest-lab-create-lab.md) om du inte vill använda ett befintligt labb för att testa skriptet eller kommandona i den här artikeln. 
- [Installera Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) eller använd Azure Cloud Shell som är integrerat i Azure-portalen. 

## <a name="powershell-script"></a>PowerShell-skript
Exempelskriptet i det här avsnittet använder cmdleten [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Den här cmdleten tar labbets resurs-ID,`createEnvironment`namnet på åtgärden för att utföra ( ) och de parametrar som krävs för att utföra den åtgärden. Parametrarna finns i en hash-tabell som innehåller alla egenskaper för beskrivning av virtuella datorer. 

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
          "expirationDate"          = "2019-12-01"
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

Egenskaperna för den virtuella datorn i skriptet ovan gör det möjligt för oss att skapa en virtuell dator med Windows Server 2016 DataCenter som operativsystem. För varje typ av virtuell dator kommer dessa egenskaper att vara något olika. Avsnittet [Definiera virtuell dator](#define-virtual-machine) visar hur du avgör vilka egenskaper som ska användas i det här skriptet.

Följande kommando är ett exempel på hur skriptet har körts i ett filnamn: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definiera virtuell dator
I det här avsnittet visas hur du hämtar de egenskaper som är specifika för en typ av virtuell dator som du vill skapa. 

### <a name="use-azure-portal"></a>Använda Azure-portalen
Du kan generera en Azure Resource Manager-mall när du skapar en virtuell dator i Azure-portalen. Du behöver inte slutföra processen med att skapa den virtuella datorn. Du följer bara stegen tills du ser mallen. Detta är det bästa sättet att få den nödvändiga JSON-beskrivningen om du inte redan har skapat en labb-VM. 

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn till vänster.
3. Sök efter och välj **DevTest Labs** i listan över tjänster. 
4. På sidan **DevTest Labs** väljer du ditt labb i listan över labb.
5. På labbets startsida väljer du **+ Lägg till** i verktygsfältet. 
6. Välj en **basavbildning** för den virtuella datorn. 
7. Välj **automatiseringsalternativ** längst ned på sidan ovanför knappen **Skicka.** 
8. Du ser **Azure Resource Manager-mallen** för att skapa den virtuella datorn. 
9. JSON-segmentet i **avsnittet Resurser** har definitionen för den bildtyp som du valde tidigare. 

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

I det här exemplet ser du hur du får en definition av en Azure Market Place-avbildning. Du kan få en definition av en anpassad bild, en formel eller en miljö på samma sätt. Lägg till alla artefakter som behövs för den virtuella datorn och ange eventuella avancerade inställningar som krävs. När du har ange värden för de obligatoriska fälten och eventuella valfria fält innan du väljer knappen **Alternativ för automatisering.**

### <a name="use-azure-rest-api"></a>Använda Azure REST API
Följande procedur ger dig steg för att hämta egenskaper för en avbildning med hjälp av REST API: Dessa steg fungerar endast för en befintlig virtuell dator i ett labb. 

1. Navigera till den [virtuella datorer - lista](/rest/api/dtl/virtualmachines/list) sida, välj Prova **den** knappen. 
2. Välj din **Azure-prenumeration**.
3. Ange **resursgruppen för labbet**.
4. Ange **namnet på labbet**. 
5. Välj **Kör**.
6. Du ser **egenskaperna för avbildningen** baserat på vilken den virtuella datorn skapades. 

## <a name="set-expiration-date"></a>Ange utgångsdatum
I scenarier som utbildning, demonstrationer och utvärderingsversioner kanske du vill skapa virtuella datorer och ta bort dem automatiskt efter en fast varaktighet så att du inte ådrar dig onödiga kostnader. Du kan ange ett utgångsdatum för en virtuell dator när du skapar det med PowerShell som visas i exemplet [PowerShell-skriptavsnittet.](#powershell-script)

Här är ett exempel på PowerShell-skript som anger utgångsdatum för alla befintliga virtuella datorer i ett labb:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure PowerShell-dokumentation för Azure DevTest Labs](/powershell/module/az.devtestlabs/)
