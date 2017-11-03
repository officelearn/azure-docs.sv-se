---
title: "Förstå hur du skapar UI definition för Azure hanterade program | Microsoft Docs"
description: "Beskriver hur du skapar UI definitioner för hanterade program i Azure"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d8f04d8ed2e56cecb1b7a850bed55a02a9492bb5
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Skapa Azure portal användargränssnittet för det hanterade programmet
Det här dokumentet introducerar grundbegrepp i filen createUiDefinition.json. Azure-portalen använder den här filen för att generera användargränssnittet för att skapa en hanterad App.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
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

* hanterare
* Version
* parameters

För hanterade program hanterare ska alltid vara `Microsoft.Compute.MultiVm`, och den senaste versionen är `0.1.2-preview`.

Schemat för egenskapen parameters är beroende av en kombination av den angivna hanteraren och version. För hanterade program egenskaperna som stöds är `basics`, `steps`, och `outputs`. Grunderna och steg egenskaper innehåller den _element_ - som textrutor och nedrullningsbara listorna - ska visas i Azure-portalen. Egenskapen utdata används för att mappa utdatavärden för de angivna elementen till parametrarna i mallen för distribution av Azure Resource Manager.

Inklusive `$schema` rekommenderade, men är valfritt. Om värdet för `version` måste matcha versionen i den `$schema` URI.

## <a name="basics"></a>Grundläggande inställningar
Steget grunderna är alltid det första steget i guiden skapas när Azure-portalen Parsar filen. Förutom att visa de element som anges i `basics`, portalen lägger in element för användare att välja den prenumeration, resursgrupp och plats för distributionen. I allmänhet ska element som frågar efter distributionen hela parametrar som namnet på ett kluster eller administratör autentiseringsuppgifter gå i det här steget.

Om ett element beteende beror på användarens prenumeration, resursgrupp eller plats, kan elementet inte användas i grunderna. Till exempel **Microsoft.Compute.SizeSelector** beror på användarens prenumeration och plats för att fastställa listan med tillgängliga storlekar. Därför **Microsoft.Compute.SizeSelector** kan bara användas i steg. I allmänhet endast element i den **Microsoft.Common** namnområde kan användas i grunderna. Även om vissa element i andra namnområden (t.ex. **Microsoft.Compute.Credentials**) som inte är beroende av användarens kontext, fortfarande är tillåtna.

## <a name="steps"></a>Steg
Egenskapen steg kan innehålla noll eller flera ytterligare steg för att visa efter grunderna, som innehåller ett eller flera element. Överväg att lägga till steg per roll- eller nivå för det program som distribueras. Lägg exempelvis till ett steg för indata för de överordnade noderna och ett steg för arbetarnoder i ett kluster.

## <a name="outputs"></a>utdata
Azure-portalen använder den `outputs` egenskap som ska mappas element från `basics` och `steps` till parametrar i mallen för distribution av Azure Resource Manager. Nycklar för den här ordlistan är namnen i mallparametrarna och värdena är egenskaper för utdata-objekt från de refererade element.

## <a name="functions"></a>Funktioner
Liksom Mallfunktioner i Azure Resource Manager (både i syntax och funktioner), CreateUiDefinition tillhandahåller funktioner för att arbeta med elementens indata och utdata, samt funktioner, till exempel villkorlig sats.

## <a name="next-steps"></a>Nästa steg
Filen createUiDefinition.json har ett enkelt schema. Den verkliga djup kommer från alla element som stöds och funktioner. Dessa objekt beskrivs i detalj på:

- [Element](create-uidefinition-elements.md)
- [Funktioner](create-uidefinition-functions.md)

En aktuell JSON-schema för createUiDefinition finns här: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Senare kommer att vara tillgänglig på samma plats. Ersätt den `0.1.2-preview` -delen i Webbadressen och `version` värdet med versions-ID som du tänker använda. Version som för närvarande stöds identifierare är `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview`, och `0.1.2-preview`.