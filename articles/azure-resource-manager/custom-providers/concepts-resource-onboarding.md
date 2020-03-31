---
title: Registrering av resurser
description: Lär dig mer om hur du utför resursanlökar genom att använda Azure Custom Providers för att tillämpa hantering eller konfiguration på andra Azure-resurstyper.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650414"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Översikt över Azure Custom Providers-resurser

Azure Custom Providers resurs introduktion är en utökningsbarhet modell för Azure resurstyper. Det gör att du kan tillämpa åtgärder eller hantering över befintliga Azure-resurser i stor skala. Mer information finns i [Hur Azure Custom Providers kan utöka Azure](overview.md). I den här artikeln beskrivs:

- Vad resurs onboarding kan göra.
- Grunderna i resursbelöningar och hur du använder den.
- Här hittar du guider och kodexempel för att komma igång.

> [!IMPORTANT]
> Anpassade leverantörer är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett servicenivåavtal och vi rekommenderar den inte för produktionsarbetsbelastningar. Vissa funktioner kan inte stödjas eller ha begränsade funktioner.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Vad kan resurs onboarding göra?

I likhet med [azure custom providers anpassade resurser](./custom-providers-resources-endpoint-how-to.md)definierar resursanlökar ett kontrakt som proxyrorerar "onboarding"-begäranden till en slutpunkt. Till skillnad från anpassade resurser skapar resursanlökar inte en ny resurstyp. I stället tillåter det att befintliga resurstyper ska utökas. Och resursanknytning fungerar med Azure Policy, så hantering och konfiguration av resurser kan göras i stor skala. Några exempel på arbetsflöden för resursansvariga:

- Installera och hantera till virtuella datortillägg.
- Ladda upp och konfigurera standardvärden för Azure-lagringskonton.
- Aktivera grundläggande diagnostikinställningar när det ska ta en skala.

## <a name="resource-onboarding-basics"></a>Grunderna i resurstillbebygging

Du konfigurerar resursboarding via Azure Custom Providers med hjälp av Microsoft.CustomProviders/resourceProviders och Microsoft.CustomProviders/associations-resurstyper. Om du vill aktivera resursanlökar för en anpassad provider skapar du under konfigurationsprocessen en **resourceType** som kallas "associationer" med en **routingType** som innehåller "Tillägg". Microsoft.CustomProviders/associationer och Microsoft.CustomProviders/resourceProviders behöver inte tillhöra samma resursgrupp.

Här är ett exempel på en anpassad Azure-leverantör:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Egenskap | Krävs? | Beskrivning
---|---|---
namn | Ja | Namnet på slutpunktsdefinitionen. För resurstillverkning måste namnet vara "associationer".
routingType | Ja | Bestämmer typen av kontrakt med slutpunkten. För onboarding av resurser är giltiga **routingTypes** "Proxy, Cache,Extension" och "Webhook,Cache,Extension".
slutpunkt | Ja | Slutpunkten som begäranden ska dirigeras till. Detta kommer att hantera svaret och eventuella biverkningar av begäran.

När du har skapat den anpassade providern med associationsresurstypen kan du inrikta dig på Microsoft.CustomProviders/associations. Microsoft.CustomProviders/associations är en tilläggsresurs som kan utöka alla andra Azure-resurser. När en instans av Microsoft.CustomProviders/associations skapas krävs ett **egenskapsmålResourceId**, vilket bör vara ett giltigt Microsoft.CustomProviders/resourceProviders eller Microsoft.Solutions/applications resource ID. I dessa fall vidarebefordras begäran till associationsresurstypen på microsoft.CustomProviders/resourceProviders-instansen som du skapade.

> [!NOTE]
> Om ett Microsoft.Solutions/applications-resurs-ID anges som **targetResourceId**måste det finnas en Microsoft.CustomProviders/resourceProviders som distribueras i den hanterade resursgruppen med namnet "public".

Exempel på Azure Custom Providers-association:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Egenskap | Krävs? | Beskrivning
---|---|---
targetResourceId | Ja | Resurs-ID för Microsoft.CustomProviders/resourceProviders eller Microsoft.Solutions/programs.

## <a name="how-to-use-resource-onboarding"></a>Så här använder du resursboarding

Resursanlökar fungerar genom att utöka andra resurser med tilläggsresursen Microsoft.CustomProviders/associations. I följande exempel görs begäran för en virtuell dator, men alla resurser kan utökas.

Först måste du skapa en anpassad leverantörsresurs med en associationsresurstyp. Detta deklarerar motringnings-URL:en som ska användas när en motsvarande Microsoft.CustomProviders/associations-resurs skapas, som riktar sig till den anpassade providern.

Exempel på Microsoft.CustomProviders/resourceProviders skapar begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

När du har skapat den anpassade leverantören kan du rikta in dig på andra resurser och tillämpa bieffekterna av den anpassade leverantören på dem.

Exempel på Microsoft.CustomProviders/associations skapar begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Den här begäran vidarebefordras sedan till den slutpunkt som anges i den anpassade providern som du skapade, som refereras av **targetResourceId** i det här formuläret:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Slutpunkten ska svara med ett `Content-Type` program/json och en giltig JSON-svarstext. Fält som returneras under **egenskapsobjektet** för JSON läggs till i associationsretursvaret.

## <a name="getting-help"></a>Få hjälp

Om du har frågor om utveckling av Azure Custom Resource Providers kan du prova att fråga dem på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kan redan ha besvarats, så kontrollera först innan du postar. Lägg till ```azure-custom-providers``` taggen för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Se de här artiklarna om du vill veta mer:

- [Självstudiekurs: Onboarding av resurser med anpassade leverantörer](./tutorial-resource-onboarding.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Snabbstart: Skapa en anpassad resursleverantör och distribuera anpassade resurser](./create-custom-provider.md)
- [Så här lägger du till anpassade åtgärder i ett Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Så här lägger du till anpassade resurser i ett Azure REST API](./custom-providers-resources-endpoint-how-to.md)
