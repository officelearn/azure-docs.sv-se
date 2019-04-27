---
title: Förstå skapar användargränssnitt definition för Azure hanterade program | Microsoft Docs
description: Beskriver hur du skapar definitioner för användargränssnitt för Azure Managed Applications
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: ab777b487159b009bf2cac6086bb09cc71714b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587758"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Skapa Azure portalens användargränssnitt för det hanterade programmet
Det här dokumentet introducerar grundkoncepten i filen createUiDefinition.json. Azure-portalen använder den här filen för att generera användargränssnittet för att skapa ett hanterat program.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

En CreateUiDefinition innehåller alltid tre egenskaper: 

* Hanterare
* version
* parameters

För hanterade program, hanterare ska alltid vara `Microsoft.Compute.MultiVm`, och den senaste versionen är `0.1.2-preview`.

Schemat för egenskapen parameters är beroende av en kombination av den angivna hanteraren och version. För hanterade program, egenskaper som stöds är `basics`, `steps`, och `outputs`. Egenskaperna grunderna och steg innehålla den _element_ - som textrutor och listrutor – som ska visas i Azure-portalen. Utdata-egenskapen används för att mappa utdata värdena för de angivna elementen till parametrarna i mallen för distribution av Azure Resource Manager.

Inklusive `$schema` rekommenderas, men är valfritt. Om anges värdet för `version` måste matcha versionen inom den `$schema` URI.

## <a name="basics"></a>Grundläggande inställningar
Grunderna i steget är alltid det första steget i guiden som skapades när Azure-portalen Parsar filen. Förutom att visa de element som anges i `basics`, portalen lägger in element för användare att välja den prenumeration, resursgrupp och plats för distributionen. I allmänhet ska element som frågar efter distribution hela parametrar, t.ex namnet på ett kluster eller administratör autentiseringsuppgifter placeras i det här steget.

Om ett element beteende beror på användarens prenumeration, resursgrupp eller plats, kan elementet inte användas i grunderna. Till exempel **Microsoft.Compute.SizeSelector** beror på användarens prenumeration och plats för att fastställa listan med tillgängliga storlekar. Därför **Microsoft.Compute.SizeSelector** kan bara användas i steg. I allmänhet bör endast element i den **Microsoft.Common** namnområde kan användas i grunderna. Även om vissa element i andra namnområden (t.ex. **Microsoft.Compute.Credentials**) som inte beror på användarens kontext, fortfarande är tillåtna.

## <a name="steps"></a>Steg
Egenskapen stegen kan innehålla noll eller flera ytterligare åtgärder för att visa efter grunderna, vart och ett innehåller ett eller flera element. Överväg att lägga till steg per roll eller nivå av programmet som ska distribueras. Lägg exempelvis till ett steg för indata för de överordnade noderna och ett steg för arbetsnoderna i ett kluster.

## <a name="outputs"></a>Utdata
Azure-portalen använder den `outputs` egenskapen att mappa element från `basics` och `steps` till parametrarna för distributionsmallen Azure Resource Manager. Nycklarna för den här ordlistan är namnen på mallparametrarna och värdena är egenskaper för utdata-objekt från de refererade elementen.

Om du vill ange resursnamnet hanterat program, måste du inkludera ett värde med namnet `applicationResourceName` i egenskapen utdata. Om du inte anger det här värdet tilldelas ett GUID för namnet. Du kan inkludera en textruta i användargränssnittet som du begär ett namn från användaren.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Functions
Liksom Mallfunktioner i Azure Resource Manager (både i syntax och funktioner), CreateUiDefinition tillhandahåller funktioner för att arbeta med elementens indata och utdata, samt funktioner, till exempel Conditions.

## <a name="next-steps"></a>Nästa steg
CreateUiDefinition.json själva filen om du har ett enkelt schema. Det verkliga djupet på det kommer från alla element som stöds och funktioner. Dessa objekt beskrivs i detalj på:

- [Element](create-uidefinition-elements.md)
- [Funktioner](create-uidefinition-functions.md)

En aktuell JSON-schema för createUiDefinition finns här: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

En användare gränssnittet exempelfil, se [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
