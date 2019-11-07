---
title: Azure Custom providers-resurs-onboarding
description: Lär dig mer om resurs onboarding med hjälp av Azure-anpassade leverantörer för att tillämpa hantering eller konfiguration på andra Azure-resurs typer.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609156"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Översikt över resurs onboarding för Azure Custom providers

Azure Custom providers-resurs onboarding är en utöknings modell för resurs typer i Azure. Det gör att du kan använda åtgärder eller hantering över befintliga Azure-resurser i stor skala. Mer information finns i [så här kan Azure-anpassade leverantörer utöka Azure](./custom-providers-overview.md). I den här dokumentationen beskrivs:

- Vad kan en resurs onboarding göra.
- Grundläggande om resurs registrering och hur du använder det.
- Var du hittar guider och kod exempel för att komma igång.

> [!IMPORTANT]
> Anpassade providers är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Vad kan en resurs onboarding göra

I likhet med [anpassade Azure-anpassade providers-resurser](./custom-providers-resources-endpoint-how-to.md)definierar Resource onboarding ett kontrakt, som kommer att proxy-"onboarding"-förfrågningar till en slut punkt. Till skillnad från anpassade resurser skapar inte resurs onboarding en ny resurs typ, utan i stället kan tillägget av befintliga resurs typer användas. Dessutom fungerar resurs onboarding med Azure Policy, så hantering och konfiguration av resurser kan göras i stor skala. Några exempel på resurs onboarding-arbetsflöden:

- Installera och hantera på Virtual Machines-tillägg.
- Ladda upp och konfigurera standardvärden på Azure Storage-konton.
- Aktivera inställningar för bas linje diagnos i skala.

## <a name="resource-onboarding-basics"></a>Grunder för resurs registrering

Resurs onboarding konfigureras via Azure-anpassade providers med resurs typerna "Microsoft. CustomProviders/resourceProviders" och "Microsoft. CustomProviders/associationer". Om du vill aktivera resurs registrering för en anpassad Provider, under konfigurations processen, skapar du en **resourceType** med namnet "associationer" med en **routingType** som innehåller "tillägg". Dessutom behöver inte Microsoft. CustomProviders/associationer och Microsoft. CustomProviders/resourceProviders tillhöra samma resurs grupp.

Exempel på Azure-anpassad provider:

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

Egenskap | Krävs | Beskrivning
---|---|---
namn | *Ja* | Namnet på slut punkts definitionen. För resurs onboarding måste namnet vara "associationer".
routingType | *Ja* | Bestämmer kontrakts typen med **slut punkten**. För resurs onboarding är giltiga **routingTypes** "proxy, cache, Extension" och "webhook, cache, Extension".
slutpunkt | *Ja* | Slut punkten för att dirigera begär anden till. Detta hanterar svaret och eventuella sido effekter i begäran.

När den anpassade providern med resurs typen "associationer" har skapats kan du använda "Microsoft. CustomProviders/associationer". "Microsoft. CustomProviders/Associations" är en tilläggs resurs som kan utöka andra Azure-resurser. När en instans av "Microsoft. CustomProviders/associationer" skapas, tar den en egenskaps- **targetResourceId**, som ska vara ett giltigt resurs-ID "Microsoft. CustomProviders/resourceProviders" eller "Microsoft. Solutions/Applications". I dessa fall vidarebefordras begäran till resurs typen "associationer" på instansen "Microsoft. CustomProviders/resourceProviders" som vi skapade.

> [!Note]
> Om resurs-ID: t för Microsoft. Solutions/Applications anges som **targetResourceId**, måste det finnas en "Microsoft. CustomProviders/resourceProviders" distribuerad i den hanterade resurs gruppen med namnet "offentlig".

Exempel på Azures anpassade Provider-Association:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Egenskap | Krävs | Beskrivning
---|---|---
TargetResourceId | *Ja* | Resurs-ID för "Microsoft. CustomProviders/resourceProviders" eller "Microsoft. Solutions/Applications".

## <a name="how-to-use-resource-onboarding"></a>Så här använder du resurs registrering

Resurs onboarding fungerar genom att utöka andra resurser med tilläggs resursen "Microsoft. CustomProviders/associationer". I följande exempel kommer begäran att göras för en virtuell dator, men alla resurser kan utökas.

Först måste du skapa en anpassad Provider-resurs med resurs typen "associationer". Detta deklarerar återanrops-URL: en som ska användas när motsvarande "Microsoft. CustomProviders/Associations"-resurs skapas, vilket riktar sig till den anpassade providern.

Exempel på "Microsoft. CustomProviders/resourceProviders" Create förfrågan:

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

När den anpassade providern har skapats kan vi nu rikta in sig på andra resurser och använda sido effekterna av den anpassade providern.

Exempel på "Microsoft. CustomProviders/Associations" Create förfrågan:

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

Den här begäran vidarebefordras sedan till den slut punkt som anges i den första skapade anpassade providern som refereras till av "targetResourceId" i formuläret:

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

Slut punkten bör svara med "Application/JSON" `Content-Type` och giltig JSON-svars text. Fält som returneras under objektet "egenskaper" i JSON kommer att läggas till i associationens retur svar.

## <a name="looking-for-help"></a>Söker efter hjälp

Om du har frågor om utveckling av Azure anpassad resurs leverantör kan du prova med att ange [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har blivit ombeddd och besvarad, så kontrol lera först innan du publicerar. Lägg till taggen ```azure-custom-providers``` för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Gå till nästa artikel om du vill skapa en anpassad Provider.

- [Självstudie: resurs onboarding med anpassade providers](./tutorial-custom-providers-resource-onboarding.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-custom-providers-101.md)
- [Snabb start: skapa en anpassad resurs leverantör för Azure och distribuera anpassade resurser](./create-custom-provider.md)
- [Gör så här: lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Gör så här: lägga till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
