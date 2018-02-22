---
title: "Skapa mallar för Azure-stacken | Microsoft Docs"
description: "Läs Azure Stack mallen bästa praxis"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: f85875b5b128f53d45fe9af97c026fc6e34b2d27
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-manager-template-considerations"></a>Överväganden för Azure Resource Manager

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

När du utvecklar programmet är det viktigt att se till att mallen rörlighet mellan Azure och Azure-stacken. Den här artikeln innehåller överväganden för att utveckla Azure Resource Manager [mallar](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), så att du kan prototyp programmet och testa distributionen i Azure utan åtkomst till en Azure-Stack-miljö.

## <a name="resource-provider-availability"></a>Providern resurstillgänglighet
Den mall som du planerar att distribuera måste bara använda Microsoft Azure-tjänster som redan är tillgängliga eller i Förhandsgranska i Azure-stacken.

## <a name="public-namespaces"></a>Offentliga namnområden
Eftersom Azure Stack finns i ditt datacenter, har olika service endpoint namnområden än det offentliga Azure-molnet. Därför misslyckas hårdkodad offentliga slutpunkter i Azure Resource Manager-mallar när du försöker distribuera dem till Azure-stacken. Använd i stället de *referens* och *sammanfoga* funktion för att dynamiskt skapa tjänstslutpunkten baserat på värden som hämtats från resursprovidern under distributionen. Till exempel i stället för att ange *blob.core.windows.net* i mallen, hämta den [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) med den *osDisk.URI* slutpunkt:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API-version
Azure service-versioner kan variera mellan Azure och Azure-stacken. Varje resurs kräver attributet apiVersion, som definierar de funktioner som erbjuds. För att säkerställa kompatibilitet för API-version i Azure-stacken, följer giltiga API-versioner för varje Resursprovider:

| Resursprovider | apiVersion |
| --- | --- |
| Compute |`'2015-06-15'` |
| Nätverk |`'2015-06-15'`, `'2015-05-01-preview'` |
| Lagring |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Mallfunktioner
Azure Resource Manager [funktioner](../../azure-resource-manager/resource-group-template-functions.md) innehåller funktioner som krävs för att skapa dynamiska mallar. Du kan använda funktioner för uppgifter som exempelvis:

* Sammanfoga eller trimning strängar 
* Referensvärden från andra resurser
* Iteration av resurser för att distribuera flera instanser 

Dessa funktioner är inte tillgängliga i Azure Stack:

* Hoppa över
* ta

## <a name="resource-location"></a>Resursplats
Azure Resource Manager-mallar kan du använda ett platsattribut för att placera resurser under distributionen. I Azure referera platser till ett område som västra USA eller Sydamerika. Platser är olika i Azure-stacken eftersom Azure-stacken är i ditt datacenter. För att säkerställa mallar är överföringsbar mellan Azure och Azure-stacken, bör referera resursgruppens plats när du distribuerar enskilda resurser. Du kan göra detta med hjälp av `[resourceGroup().Location]` att se till att alla resurser ärver resursgruppens plats. Följande utdrag är ett exempel på hur du använder den här funktionen vid distribution av ett lagringskonto:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Nästa steg
* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)
* [Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)

