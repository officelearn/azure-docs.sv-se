---
title: Filen CreateUiDefinition.json för portalfönstret
description: Beskriver hur du skapar användargränssnittsdefinitioner för Azure-portalen. Används när du definierar Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294899"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json för Azure-upplevelsen för att skapa hanterade program

Det här dokumentet introducerar de grundläggande begreppen i filen **createUiDefinition.json** som Azure-portalen använder för att definiera användargränssnittet när du skapar ett hanterat program.

Mallen är följande

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

En CreateUiDefinition innehåller alltid tre egenskaper: 

* Handler
* version
* parameters

Hanteraren ska alltid `Microsoft.Azure.CreateUIDef`vara det och `0.1.2-preview`den senaste versionen som stöds är .

Schemat för parameteregenskapen beror på kombinationen av den angivna hanteraren och versionen. För hanterade program är `basics`de `steps`egenskaper `outputs`som stöds , och . Egenskaperna grunderna och stegen innehåller de [element](create-uidefinition-elements.md) - som textrutor och listrutor - som ska visas i Azure-portalen. Egenskapen outputs används för att mappa utdatavärdena för de angivna elementen till parametrarna för distributionsmallen för Azure Resource Manager.

Inklusive `$schema` rekommenderas, men valfritt. Om det anges måste `version` värdet för matcha `$schema` versionen i URI.If specified, the value for must match the version within the URI.

Du kan använda en JSON-redigerare för att skapa din createUiDefinition och sedan testa den i [sandlådan createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) för att förhandsgranska den. Mer information om sandlådan finns i [Testa portalgränssnittet för Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Grundläggande inställningar

Grunderna är det första steget som genereras när Azure-portalen tolkar filen. Förutom att visa de element `basics`som anges i injicerar portalen element för användare att välja prenumeration, resursgrupp och plats för distributionen. När det är möjligt bör element som frågar distributionsomfattande parametrar, till exempel namnet på ett kluster eller administratörsbehörighet, gå i det här steget.

## <a name="steps"></a>Steg

Egenskapen Steps kan innehålla noll eller fler ytterligare steg för att visa efter grunderna, som var och en innehåller ett eller flera element. Överväg att lägga till steg per roll eller nivå för det program som distribueras. Lägg till exempel till ett steg för huvudnodindata och ett steg för arbetsnoderna i ett kluster.

## <a name="outputs"></a>Utdata

Azure-portalen `outputs` använder egenskapen `basics` för `steps` att mappa element från och till parametrarna för distributionsmallen för Azure Resource Manager. Nycklarna i den här ordlistan är namnen på mallparametrarna och värdena är egenskaper för utdataobjekten från de refererade elementen.

Om du vill ange det hanterade programresursnamnet måste du inkludera ett värde som namnges `applicationResourceName` i egenskapen utdata. Om du inte anger det här värdet tilldelar programmet ett GUID för namnet. Du kan inkludera en textruta i användargränssnittet som begär ett namn från användaren.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resurstyper

Om du bara vill filtrera de tillgängliga platserna till de platser som stöder de resurstyper som ska distribueras anger du en matris med resurstyperna. Om du anger mer än en resurstyp returneras endast de platser som stöder alla resurstyper. Den här egenskapen är valfri.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funktioner

CreateUiDefinition innehåller [funktioner](create-uidefinition-functions.md) för att arbeta med elementens indata och utdata och funktioner som villkor. Dessa funktioner liknar både syntax och funktionalitet som Azure Resource Manager-mallfunktioner.

## <a name="next-steps"></a>Nästa steg

Själva filen createUiDefinition.json har ett enkelt schema. Det verkliga djupet av det kommer från alla element och funktioner som stöds. Dessa poster beskrivs mer i detalj på:

- [Element](create-uidefinition-elements.md)
- [Funktioner](create-uidefinition-functions.md)

Ett aktuellt JSON-schema för createUiDefinition finns här: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

En exempelanvändargränssnittsfil finns i [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
