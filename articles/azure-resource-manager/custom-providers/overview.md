---
title: Översikt över anpassade leverantörer
description: Lär dig mer om Azure Custom Resource Providers och hur du utökar Azure API-planet så att det passar dina arbetsflöden.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398466"
---
# <a name="azure-custom-resource-providers-overview"></a>Översikt över Azure Custom Resource Providers

Azure Custom Resource Providers är en utökningsplattform till Azure. Det låter dig definiera anpassade API:er som kan användas för att berika standard azure-upplevelsen. I den här dokumentationen beskrivs:

- Så här skapar och distribuerar du en Azure Custom Resource Provider.
- Så här använder du Azure Custom Resource Providers för att utöka befintliga arbetsflöden.
- Här hittar du guider och kodexempel för att komma igång.

![Översikt över anpassade leverantörer](./media/overview/overview.png)

> [!IMPORTANT]
> Anpassade leverantörer är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Vad kan anpassade resursleverantörer göra

Här är några exempel på vad du kan uppnå med Azure Custom Resource Providers:

- Utöka AZURE Resource Manager REST API för att inkludera interna och externa tjänster.
- Aktivera anpassade scenarier ovanpå befintliga Azure-arbetsflöden.
- Anpassa kontroll och effekt för Azure Resource Manager-mallar.

## <a name="what-is-a-custom-resource-provider"></a>Vad är en anpassad resursleverantör

Azure Custom Resource Providers skapas genom att skapa ett kontrakt mellan Azure och en slutpunkt. Det här kontraktet definierar en lista över nya resurser och åtgärder via en ny resurs, **Microsoft.CustomProviders/resourceProviders**. Den anpassade resursleverantören exponerar sedan dessa nya API:er i Azure. Azure Custom Resource Providers består av tre delar: anpassad resursleverantör, **slutpunkter**och anpassade resurser.

## <a name="how-to-build-custom-resource-providers"></a>Så här skapar du anpassade resursleverantörer

Anpassade resursleverantörer är en lista över kontrakt mellan Azure och slutpunkter. Det här kontraktet beskriver hur Azure ska interagera med en slutpunkt. Resursleverantören fungerar som en proxy och vidarebefordrar begäranden och svar till och från den angivna **slutpunkten**. En resursprovider kan ange två typer av kontrakt: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) och [**åtgärder**](./custom-providers-action-endpoint-how-to.md). Dessa aktiveras genom slutpunktsdefinitioner. En slutpunktsdefinition består av tre fält: **namn,** **routingType**och **slutpunkt**.

Exempel på slutpunkt:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
namn | *Ja* | Namnet på slutpunktsdefinitionen. Azure exponerar det här namnet via dess API under '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *nej* | Bestämmer kontraktstypen med **slutpunkten**. Om det inte anges, kommer det att standard till "Proxy".
slutpunkt | *Ja* | Slutpunkten som begäranden ska dirigeras till. Detta kommer att hantera svaret samt eventuella biverkningar av begäran.

### <a name="building-custom-resources"></a>Skapa anpassade resurser

**ResourceTypes** beskriver nya anpassade resurser som läggs till i Azure. Dessa exponera grundläggande RESTful CRUD metoder. Läs [mer om hur du skapar anpassade resurser](./custom-providers-resources-endpoint-how-to.md)

Exempel på anpassad resursprovider med **resourceTypes:**

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

API:er som lagts till i Azure för ovanstående exempel:

Mer från HttpMethod | Exempel på URI | Beskrivning
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anrop för att skapa en ny resurs.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anropet för att ta bort en befintlig resurs.
HÄMTA | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API-anropet för att hämta en befintlig resurs.
HÄMTA | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API-anrop för att hämta listan över befintliga resurser.

### <a name="building-custom-actions"></a>Skapa anpassade åtgärder

**Åtgärder** beskriver nya åtgärder som läggs till i Azure. Dessa kan exponeras ovanpå resursprovidern eller kapslas under en **resourceType**. Läs [mer om hur du skapar anpassade åtgärder](./custom-providers-action-endpoint-how-to.md)

Exempel på anpassad resursprovider med **åtgärder:**

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

API:er som lagts till i Azure för ovanstående exempel:

Mer från HttpMethod | Exempel på URI | Beskrivning
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Azure REST API-anrop för att aktivera åtgärden.

## <a name="looking-for-help"></a>Söker hjälp

Om du har frågor om utveckling av Azure Custom Resource Provider kan du prova att fråga på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kan redan ha ställts och besvarats, så kontrollera först innan du postar. Lägg till ```azure-custom-providers``` taggen för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Gå till nästa artikel för att skapa en anpassad leverantör.

- [Snabbstart: Skapa Azure Custom Resource Provider och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Så här lägger du till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Så här lägger du till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
