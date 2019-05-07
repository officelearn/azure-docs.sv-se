---
title: Översikt över Azure anpassade Providers förhandsversion
description: Beskriver konceptet för att skapa en anpassad resurs-provider med Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159862"
---
# <a name="azure-custom-providers-preview-overview"></a>Översikt över Azure anpassade Providers förhandsversion

Med Azure anpassade Providers, kan du utöka Azure för att arbeta med din tjänst. Du skapar en egen resource provider, inklusive anpassade resurstyperna och åtgärderna. Den anpassade providern är integrerad med Azure Resource Manager. Du kan använda Resource Manager-funktioner, till exempel malldistributioner och rollbaserad åtkomstkontroll för att distribuera och skydda din tjänst.

Den här artikeln innehåller en översikt över anpassade providers och dess funktioner. Följande bild visar arbetsflödet för resurser och åtgärder som definierats i en anpassad provider.

![Översikt över anpassad provider](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Anpassade Providers är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definiera den anpassade providern

Du startar genom att låta Azure Resource Manager veta om den anpassade providern. Du distribuerar till Azure en anpassad provider-resurs, som använder resurstypen för **Microsoft.CustomProviders/resourceProviders**. I resursen måste definiera du resurser och åtgärder för din tjänst.

Om din tjänst måste en resurstyp som heter exempelvis **användare**, du inkluderar den resurstypen i anpassad provider-definition. För varje resurstyp tillhandahåller du en slutpunkt som erbjuder REST-åtgärder (PUT, hämta, ta bort) för den resurstypen. Slutpunkten kan köras på alla miljöer och innehåller logik för hanteringen av åtgärder på resurstypen i din tjänst.

Du kan också definiera anpassade åtgärder för din resursprovider. Åtgärder som representerar POST-åtgärder. Använda åtgärder för åtgärder som till exempel starta, stoppa eller starta om. Du kan ange en slutpunkt som hanterar begäran.

I följande exempel visas hur du definierar en anpassad provider med en åtgärd och en resurstyp.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

För **routingType**, de godkända värdena är `Proxy` och `Cache`. Proxy innebär att begäranden för resurstypen eller åtgärden hanteras av slutpunkten. Cache-inställningen stöds endast för resurstyper, inte åtgärder. Om du vill ange cache, måste du även ange proxy. Cache innebär lagras svaren från slutpunkten för att optimera läsåtgärder. Med hjälp av cache-inställningen gör det enklare att implementera en API som är konsekvent och kompatibel med andra Resource Manager-tjänster.

## <a name="deploy-your-resource-types"></a>Distribuera din resurstyper

När du har definierat den anpassade providern, kan du distribuera dina anpassade resurstyper. I följande exempel visar JSON som du inkluderar i mallen för att distribuera resurstypen för den anpassade providern. Den här resurstypen kan distribueras i samma mall som med andra Azure-resurser.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Hantera åtkomst

Använd Azure [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) att hantera åtkomst till din resursprovider. Du kan tilldela [inbyggda roller](../role-based-access-control/built-in-roles.md) som ägare, deltagare eller läsare för användare. Eller, du kan definiera [anpassade roller](../role-based-access-control/custom-roles.md) som är specifika för åtgärderna i din resursprovider.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade providers. Gå till nästa artikel för att skapa en anpassad provider.

> [!div class="nextstepaction"]
> [Självstudie: Skapa anpassad provider och distribuera anpassade resurser](create-custom-provider.md)
