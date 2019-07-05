---
title: Översikt över Azure anpassade Resursprovidrar
description: Läs mer om Azure anpassade Resursprovidrar och hur du utökar Azure API-plan för att passa dina arbetsflöden.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: f418cd6c5470740ce123448ddbbe54cb6e89dabe
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67475949"
---
# <a name="azure-custom-resource-providers-overview"></a>Översikt över Azure anpassad resurs-Providers

Azure anpassade Resource Providers är en plattform för utökningsbarhet till Azure. Det kan du definiera anpassade API: er som kan användas för att utöka standard Azure-upplevelse. Den här dokumentationen beskrivs:

- Så här att skapa och distribuera en Azure Resource Provider för anpassad.
- Hur du använder Azure anpassade Resource Providers för att utöka befintliga arbetsflöden.
- Var du hittar guider och kodexempel för att komma igång.

![Översikt över anpassad provider](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Anpassade Providers är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Vad kan anpassade resursprovidrar?

Här följer några exempel på vad du kan uppnå med Azure anpassade Resource Providers:

- Utöka Azure Resource Manager REST API för att inkludera interna och externa tjänster.
- Aktivera anpassade scenarier på befintliga Azure-arbetsflöden.
- Anpassa Azure Resource Manager-mallar kontroll och effekt.

## <a name="what-is-a-custom-resource-provider"></a>Vad är en anpassad resurs-provider

Azure anpassad Resursprovidrar görs genom att skapa ett avtal mellan Azure och en slutpunkt. Det här kontraktet definierar en lista över nya resurser och åtgärder via en ny resurs, **Microsoft.CustomProviders/resourceProviders**. Anpassade resursprovidern utsätter sedan dessa nya API: er i Azure. Azure anpassade Resursprovidrar består av tre delar: anpassade resursprovidern **slutpunkter**, och anpassade resurser.

## <a name="how-to-build-custom-resource-providers"></a>Hur du skapar anpassade resursprovidrar

Anpassade resursproviders är en lista över avtal mellan Azure och slutpunkter. Det här avtalet beskriver hur Azure ska samverka med en slutpunkt. Resurs-providern fungerar som en proxy och vidarebefordrar begäranden och svar till och från den angivna **endpoint**. En resursprovider kan ange två typer av kontrakt: [ **resurstyper** ](./custom-providers-resources-endpoint-how-to.md) och [ **åtgärder**](./custom-providers-action-endpoint-how-to.md). De aktiveras via slutpunktsdefinitionerna. En slutpunktsdefinition består av tre fält: **namn**, **routingType**, och **endpoint**.

Exemplet slutpunkt:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
name | *Ja* | Namnet på slutpunktsdefinitionen. Azure visas detta namn via dess API under ”/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Anger kontraktstypen med den **endpoint**. Om inte anges kommer det standard ”Proxy”.
endpoint | *Ja* | Slutpunkt för att dirigera begäranden till. Det hanterar svaret samt effekterna på serversidan av begäran.

### <a name="building-custom-resources"></a>Att skapa anpassade resurser

**Resurstyper** Beskriver nya anpassade resurser som läggs till Azure. Dessa exponera grundläggande RESTful CRUD-metoder. Se [mer om hur du skapar anpassade resurser](./custom-providers-resources-endpoint-how-to.md)

Exempel på anpassade Resursprovidern med **resurstyper**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API: er som har lagts till i Azure för exemplet ovan:

HttpMethod | Exemplet URI | Beskrivning
---|---|---
PLACERA | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anropet till att skapa en ny resurs.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anrop att ta bort en befintlig resurs.
HÄMTA | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anropet att hämta en befintlig resurs.
HÄMTA | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API-anrop till hämta listan över befintliga resurser.

### <a name="building-custom-actions"></a>Att skapa anpassade åtgärder

**Åtgärder** Beskriver nya åtgärder som läggs till Azure. Dessa kan visas på resursprovidern eller kapslat under en **resourceType**. Se [mer om hur du skapar anpassade åtgärder](./custom-providers-action-endpoint-how-to.md)

Exempel på anpassade Resursprovidern med **åtgärder**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API: er som har lagts till i Azure för exemplet ovan:

HttpMethod | Exemplet URI | Beskrivning
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Azure REST API-anrop till aktivera åtgärden.

## <a name="looking-for-help"></a>Behöver hjälp

Om du har frågor för Azure Resource Provider för anpassad utveckling fråga [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske har redan och svar och besvarade, så kontrollera först innan bokföring. Lägga till taggen ```azure-custom-providers``` att få snabba svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade providers. Gå till nästa artikel för att skapa en anpassad provider.

- [Självstudie: Skapa anpassade Azure-Resursprovidern och distribuera anpassade resurser](./create-custom-provider.md)
- [Anvisningar: Att lägga till anpassade åtgärder i Azure REST-API](./custom-providers-action-endpoint-how-to.md)
- [Anvisningar: Att lägga till anpassade resurser i Azure REST-API](./custom-providers-resources-endpoint-how-to.md)
