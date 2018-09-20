---
title: Utveckla mallar för Azure Stack | Microsoft Docs
description: Lär dig Metodtips för Azure Stack-mall
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 21fd3a33181542d86eccc4292ae68f7ce25e0a05
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366734"
---
# <a name="azure-resource-manager-template-considerations"></a>Överväganden för Azure Resource Manager-mall

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

När du utvecklar dina program är det viktigt att se till att mallen portabilitet mellan Azure och Azure Stack. Den här artikeln innehåller överväganden vid utveckling av Azure Resource Manager [mallar](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), så att du kan skapa prototyper program och testa distributionen i Azure utan åtkomst till en Azure Stack-miljö.

## <a name="resource-provider-availability"></a>Providern resurstillgänglighet

Den mall som du planerar att distribuera måste bara använda Microsoft Azure-tjänster som redan är tillgängligt eller i en förhandsversion i Azure Stack.

## <a name="public-namespaces"></a>Offentliga namnområden

Eftersom Azure Stack finns i ditt datacenter, har olika service endpoint namnområden än i Azures offentliga moln. Därför misslyckas hårdkodad offentliga slutpunkter i Azure Resource Manager-mallar när du försöker distribuera dem till Azure Stack. Du kan dynamiskt skapa tjänstslutpunkter med den *referens* och *sammanfoga* funktioner för att hämta värden från resursprovidern under distributionen. Till exempel i stället för hardcoding *blob.core.windows.net* i mallen, hämta den [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) med den *osDisk.URI* slutpunkt:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API-versionshantering

Azure tjänstversioner kan variera mellan Azure och Azure Stack. Varje resurs kräver den **apiVersion** attribut, som definierar de funktioner som erbjuds. För att säkerställa kompatibilitet för API-version i Azure Stack, gäller följande API-versioner för varje resursprovider:

| Resursprovider | apiVersion |
| --- | --- |
| Compute |`'2015-06-15'` |
| Nätverk |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Mallfunktioner

Azure Resource Manager [functions](../../azure-resource-manager/resource-group-template-functions.md) innehåller funktioner som krävs för att skapa dynamiska mallar. Exempelvis kan använda du funktioner för uppgifter som:

* Sammanfoga eller ta strängar.
* Refererar till värden från andra resurser.
* Iterera på resurser för att distribuera flera instanser.

Dessa funktioner är inte tillgängliga i Azure Stack:

* Hoppa över
* ta

## <a name="resource-location"></a>Resursplats

Azure Resource Manager-mallar använder en `location` attribut för att placera resurser under distributionen. I Azure avser platser en region, till exempel USA, västra eller Sydamerika. Platser är olika i Azure Stack, eftersom Azure Stack är i ditt datacenter. För att säkerställa mallar är överföras mellan Azure och Azure Stack, bör du referera till resursgruppens plats när du distribuerar enskilda resurser. Du kan göra detta med hjälp av `[resourceGroup().Location]` att se till att alla resurser ärver plats för resursgruppen. Följande kod är ett exempel på hur du använder den här funktionen när du distribuerar ett storage-konto:

```json
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
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)
* [Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
