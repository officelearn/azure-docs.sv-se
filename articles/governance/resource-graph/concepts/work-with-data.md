---
title: Arbeta med stora datamängder
description: Förstå hur du hämtar, formaterar, sidorar och hoppar över poster i stora datauppsättningar när du arbetar med Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064749"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Arbeta med stora Azure-resursdatauppsättningar

Azure Resource Graph är utformat för att arbeta med och få information om resurser i din Azure-miljö. Resource Graph gör det snabbt att få dessa data, även när du frågar tusentals poster. Resource Graph har flera alternativ för att arbeta med dessa stora datauppsättningar.

Information om hur du arbetar med frågor med hög frekvens finns i [Vägledning för begränsade begäranden](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Resultatstorlek för datauppsättning

Som standard begränsar Resursdiagram alla frågor till att returnera endast **100** poster. Den här kontrollen skyddar både användaren och tjänsten från oavsiktliga frågor som skulle resultera i stora datauppsättningar. Den här händelsen inträffar oftast när en kund experimenterar med frågor för att hitta och filtrera resurser på det sätt som passar deras särskilda behov. Den här kontrollen skiljer sig från att använda de [översta](/azure/kusto/query/topoperator) eller [begränsa](/azure/kusto/query/limitoperator) Azure Data Explorer-språkoperatörer för att begränsa resultaten.

> [!NOTE]
> När du använder **Första**rekommenderas att du beställer resultaten `asc` `desc`med minst en kolumn med eller . Utan sortering är de returnerade resultaten slumpmässiga och kan inte upprepas.

Standardgränsen kan åsidosättas genom alla metoder för att interagera med Resursdiagram. Följande exempel visar hur du ändrar storleksgränsen för datauppsättningen till _200:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

I [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)är kontrollen **$top** och är en del av **QueryRequestOptions**.

Den kontroll som är _mest restriktiv_ vinner. Om frågan till exempel använder de **översta** eller **limit-operatorerna** och skulle resultera i fler poster än **Först,** är de högsta posterna som returneras lika med **Först**. På samma sätt, om **över-** eller **gräns är** mindre än **Först,** skulle den postuppsättning som returneras vara det mindre värdet som konfigurerats av **över eller** **gräns**.

**Först** har för närvarande ett högsta tillåtna värde _på 5000_.

## <a name="skipping-records"></a>Hoppa över poster

Nästa alternativ för att arbeta med stora datauppsättningar är **skip-kontrollen.** Med den här kontrollen kan frågan hoppa över eller hoppa över det definierade antalet poster innan resultaten returneras. **Skip** är användbart för frågor som sorterar resultat på ett meningsfullt sätt där avsikten är att komma åt poster någonstans i mitten av resultatuppsättningen. Om resultaten som behövs är i slutet av den returnerade datauppsättningen är det mer effektivt att använda en annan sorteringskonfiguration och hämta resultaten från datauppsättningens överkant i stället.

> [!NOTE]
> När du använder **Hoppa rekommenderas**att resultatet beställers `asc` med `desc`minst en kolumn med eller . Utan sortering är de returnerade resultaten slumpmässiga och kan inte upprepas.

Följande exempel visar hur du hoppar över de första _10_ posterna som en fråga skulle resultera i, i stället startar den returnerade resultatuppsättningen med den elfte posten:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

I [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)är kontrollen **$skip** och är en del av **QueryRequestOptions**.

## <a name="paging-results"></a>Växla resultat

När det är nödvändigt att bryta en resultatuppsättning i mindre uppsättningar poster för bearbetning eller på grund av att en resultatuppsättning skulle överskrida det högsta tillåtna värdet _på 1000_ returnerade poster använder du växling. [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** innehåller värden som anger att en resultatuppsättning har brutits upp: **resultTruncated** och **$skipToken**.
**resultatKommenterat** är ett booleskt värde som informerar konsumenten om det finns ytterligare poster som inte returneras i svaret. Det här villkoret kan också identifieras när **egenskapen count** är mindre än egenskapen **totalRecords.** **totalRecords** definierar hur många poster som matchar frågan.

 **resultatetStämda** **är sant** när antingen växling är `id` inaktiverad eller inte möjlig på grund av ingen kolumn eller när det finns mindre resurser tillgängliga än en fråga begär. När **resultatetSträckt** är **sant**anges inte **egenskapen $skipToken.**

Följande exempel visar hur **du hoppar över** de första 3 000 posterna och returnerar de **första** 1 000 posterna efter att de poster som hoppas över azure CLI och Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Frågan måste **projicera** **id-fältet** för att sidnumrering ska fungera. Om det saknas i frågan kommer svaret inte att innehålla **$skipToken**.

En exempelvis se [Fråga på nästa sida](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) i REST API-dokumenten.

## <a name="formatting-results"></a>Formatera resultat

Resultatet av en resursdiagramfråga finns i två format, _Tabell_ och _ObjectArray_. Formatet konfigureras med parametern **resultFormat** som en del av alternativen för begäran. _Tabellformatet_ är standardvärdet för **resultFormat**.

Resultat från Azure CLI tillhandahålls som standard i JSON. Resultat i Azure PowerShell är som standard ett **PSCustomObject,** men de `ConvertTo-Json` kan snabbt konverteras till JSON med hjälp av cmdleten. För andra SDK:er kan frågeresultatet konfigureras för att mata ut _ObjectArray-formatet._

### <a name="format---table"></a>Format - Tabell

Standardformatet _Tabell_returnerar resultat i ett JSON-format som är utformat för att markera kolumndesignen och radvärdena för de egenskaper som returneras av frågan. Det här formatet liknar data enligt definitionen i en strukturerad tabell eller ett kalkylblad med de kolumner som först identifieras och sedan varje rad som representerar data som är justerade med dessa kolumner.

Här är ett exempel på _Table_ ett frågeresultat med tabellformateringen:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format - ObjectArray

_ObjectArray-formatet_ returnerar också resultat i ett JSON-format. Den här designen justerar dock till den nyckel-/värdeparrelation som är vanlig i JSON där kolumnen och raddata matchas i matrisgrupper.

Här är ett exempel på ett frågeresultat med _ObjectArray-formateringen:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Här är några exempel på att ange **resultatFormat** för att använda _ObjectArray-formatet:_

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Starter-frågor](../samples/starter.md).
- Se avancerade användningsområden i [avancerade frågor](../samples/advanced.md).
- Läs mer om hur du [utforskar resurser](explore-resources.md).