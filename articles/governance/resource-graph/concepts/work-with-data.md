---
title: Arbeta med stora datamängder
description: Lär dig hur du hämtar och styr stora data uppsättningar när du arbetar med Azures resurs diagram.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 4da890a5ef7acb44d0e8628dc4ec3904f6a065e4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980329"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Arbeta med stora Azure-resurs data uppsättningar

Azure Resource Graph är utformat för att arbeta med och få information om resurser i din Azure-miljö. Resurs diagram gör det enkelt att komma åt dessa data, även när du frågar efter tusentals poster. Resurs diagram har flera alternativ för att arbeta med dessa stora data mängder.

Vägledning om hur du arbetar med frågor med hög frekvens finns i [rikt linjer för begränsade begär Anden](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Resultat storlek för data uppsättning

Som standard begränsar resurs diagram alla frågor till att bara returnera **100** poster. Den här kontrollen skyddar både användaren och tjänsten från oavsiktliga frågor som leder till stora data mängder. Den här händelsen inträffar oftast när en kund experimenterar med frågor för att hitta och filtrera resurser på det sätt som passar deras specifika behov. Den här kontrollen skiljer sig från att använda [Top](/azure/kusto/query/topoperator) -eller [limit](/azure/kusto/query/limitoperator) -språkDatautforskarens språk operatörer för att begränsa resultaten.

> [!NOTE]
> När du använder **första gången**rekommenderar vi att du sorterar resultaten efter minst en kolumn med `asc` eller `desc`. Utan sortering är de resultat som returneras slumpmässiga och inte repeterbara.

Standard gränsen kan åsidosättas genom alla metoder för att interagera med resurs diagram. I följande exempel visas hur du ändrar storleks gränsen för data uppsättningen till _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

I [REST API](/rest/api/azureresourcegraph/resources/resources)är kontrollen **$Top** och ingår i **QueryRequestOptions**.

Den kontroll som är _mest restriktiv_ är att vinna. Om din fråga till exempel använder **Top** -eller **Limit** -operatorer och skulle resultera i fler poster än den **första**, skulle de maximala poster som returneras vara lika med **först**. På samma sätt, om **Top** eller **Limit** är mindre än den **första**, skulle den returnerade post mängden vara det mindre värdet som kon figurer ATS av **Top** eller **Limit**.

För **närvarande har** det högsta tillåtna värdet _5000_.

## <a name="skipping-records"></a>Hoppar över poster

Nästa alternativ för att arbeta med stora data mängder är **Skip** -kontrollen. Den här kontrollen tillåter att din fråga hoppar över eller hoppar över det definierade antalet poster innan resultatet returneras. **Skip** är användbart för frågor som sorterar resultat på ett meningsfullt sätt där avsikten är att hämta poster någonstans mitt i resultat uppsättningen. Om de resultat som behövs finns i slutet av den returnerade data uppsättningen, är det mer effektivt att använda en annan sorterings konfiguration och hämta resultaten från data uppsättningens överkant i stället.

> [!NOTE]
> När du använder **Skip**rekommenderar vi att du sorterar resultaten efter minst en kolumn med `asc` eller `desc`. Utan sortering är de resultat som returneras slumpmässiga och inte repeterbara.

I följande exempel visas hur du hoppar över de första _10_ posterna som en fråga resulterar i, i stället för att starta den returnerade resultat uppsättningen med den elfte posten:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

I [REST API](/rest/api/azureresourcegraph/resources/resources)är kontrollen **$Skip** och ingår i **QueryRequestOptions**.

## <a name="paging-results"></a>Växla resultat

När det är nödvändigt att dela upp en resultat uppsättning i mindre mängder poster för bearbetning eller eftersom en resultat uppsättning skulle överskrida det högsta tillåtna värdet för _1000_ returnerade poster, använder du sid indelning. [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** innehåller värden för att indikera en resultat uppsättning har delats upp: **resultTruncated** och **$skipToken**.
**resultTruncated** är ett booleskt värde som informerar konsumenten om det finns ytterligare poster som inte returneras i svaret. Det här villkoret kan också identifieras när **Count** -egenskapen är mindre än egenskapen **totalRecords** . **totalRecords** definierar hur många poster som matchar frågan.

När **resultTruncated** är **true**anges egenskapen **$skipToken** i svaret. Det här värdet används med samma fråge-och prenumerations värden för att hämta nästa uppsättning poster som matchar frågan.

I följande exempel visas hur du **hoppar över** de första 3000 posterna och returnerar de **första** 1000-posterna när de hoppades över med Azure CLI och Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Frågan måste **projicera** fältet **ID** för att sid brytning ska fungera. Om det saknas i frågan, innehåller svaret inte **$skipToken**.

Ett exempel finns i [Nästa sida fråga](/rest/api/azureresourcegraph/resources/resources#next-page-query) i REST API dokumenten.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig att [utforska resurser](explore-resources.md).