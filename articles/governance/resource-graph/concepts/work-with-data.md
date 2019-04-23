---
title: Arbeta med stora datamängder
description: Förstå hur du hämtar och styra stora uppsättningar data när du arbetar med Azure Resource-diagram.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 729e9fe749212942c6dc18fc7d6301934e7dd184
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788246"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Arbeta med stora Azure-resurs datauppsättningar

Azure Resource Graph är utformad för att arbeta med och få information om resurser i Azure-miljön. Resursen Graph gör hämta dessa data snabbt, även vid frågor till tusentals poster. Resursdiagrammet har flera alternativ för att arbeta med dessa stora datamängder.

## <a name="data-set-result-size"></a>Datauppsättning storlek

Som standard begränsar Resource Graph någon fråga för att returnera endast **100** poster. Den här kontrollen skyddar både användaren och tjänsten mot oavsiktlig frågor som resulterar i stora datamängder. Den här händelsen inträffar oftast när en kund experimentera med frågor att söka efter och filtrera resurser i det sätt som passar deras specifika behov. Den här kontrollen är annorlunda än att använda den [upp](/azure/kusto/query/topoperator) eller [gränsen](/azure/kusto/query/limitoperator) Azure Data Explorer språk operatörer att begränsa resultatet.

> [!NOTE]
> När du använder **första**, rekommenderas du för att sortera efter till minst en kolumn med `asc` eller `desc`. Utan att sortera är resultatet som returneras slumpmässig och inte repeterbara.

Standardgränsen kan åsidosättas med alla metoder för interaktion med resursen Graph. I följande exempel visas hur du ändrar storleksgräns för datauppsättning till _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

I den [REST API](/rest/api/azureresourcegraph/resources/resources), kontrollen är **$top** och är en del av **QueryRequestOptions**.

Den kontroll som är _mest restriktiva_ vinner. Exempel: om din fråga använder den **upp** eller **gränsen** operatorer och resulterar i fler poster än **första**, maximalt antal poster returneras skulle vara lika med **Första**. På samma sätt om **upp** eller **gränsen** är mindre än **första**, postuppsättningen returnerade blir det mindre värdet som konfigurerats av **upp** eller **gränsen**.

**Första** för närvarande har en högsta tillåtna värdet på _5000_.

## <a name="skipping-records"></a>Hoppar över poster

Nästa alternativ för att arbeta med stora datauppsättningar är den **hoppa över** kontroll. Den här kontrollen kan din fråga för att hoppa över eller hoppa över det angivna antalet poster innan det returneras resultatet. **Hoppa över** är användbart för frågor som sortera resultaten på ett meningsfullt sätt där avsikten är att hämta vid poster någonstans mitt i resultatmängden. Om resultatet som behövs är i slutet av den returnerade datauppsättningen, är det mer effektivt att använda en annan sortering konfiguration och hämta resultaten från början av en uppsättning data i stället.

> [!NOTE]
> När du använder **hoppa över**, rekommenderas du för att sortera efter till minst en kolumn med `asc` eller `desc`. Utan att sortera är resultatet som returneras slumpmässig och inte repeterbara.

I följande exempel visas hur du hoppa över först _10_ poster som en fråga skulle resultera i, i stället startar det returnerade resultatet anges med den 11: e-posten:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

I den [REST API](/rest/api/azureresourcegraph/resources/resources), kontrollen är **$skip** och är en del av **QueryRequestOptions**.

## <a name="paging-results"></a>Växla resultat

När det är nödvändigt att avbryta en resultatmängd i mindre uppsättningar av poster för bearbetning eller på grund av en resultatmängd skulle överskrida det högsta tillåtna värdet för _1000_ returnerade poster, använda växling. Den [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** innehåller värden för att ange en uppsättning har delats upp resultat: **resultTruncated** och **$skipToken** .
**resultTruncated** är ett booleskt värde som informerar användaren om det finns ytterligare poster inte returneras i svaret. Det här tillståndet kan också vara identifieras när den **antal** egenskapen är mindre än värdet **totalRecords** egenskapen. **totalRecords** definierar hur många poster som matchar frågan.

När **resultTruncated** är **SANT**, **$skipToken** egenskapen är inställda i svaret. Det här värdet används med värdena för samma fråga och -prenumeration för att hämta nästa uppsättning poster som matchar frågan.

> [!IMPORTANT]
> Frågan måste **projekt** den **id** fältet för sidbrytning ska fungera. Om den saknas från frågan, REST API-svaret inte innehålla den **$skipToken**.

Ett exempel finns i [nästa sida fråga](/rest/api/azureresourcegraph/resources/resources#next_page_query) i REST API-dokument.

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
- Lär dig att [utforska resurser](explore-resources.md)