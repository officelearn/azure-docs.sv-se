---
title: Säkerhetsfilter för att trimma resultat – Azure Search
description: Åtkomstkontroll på Azure Search-innehåll med säkerhetsfilter och användaridentiteter.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 326a449d3992d22a4be2d365061c99ef8b13aef9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453498"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Säkerhetsfilter för trimning resultat i Azure Search

Du kan tillämpa säkerhetsfilter för att trimma sökresultat i Azure Search baserat på användarens identitet. Den här sökupplevelse kräver vanligtvis jämföra identiteten för den begär sökningen mot ett fält som innehåller de principer som har behörighet till dokumentet. När en matchning hittas, har användaren eller huvudnamn (till exempel en grupp eller roll) du åtkomst till dokumentet.

Ett sätt att uppnå security filtrering är via en komplicerad disjunktion av likhet uttryck: till exempel `Id eq 'id1' or Id eq 'id2'`, och så vidare. Den här metoden är felbenägna, det är svårt att underhålla, och minskar svarstiden för frågorna med många sekunder i fall där listan innehåller hundratals eller tusentals värden. 

Ett enklare och snabbare sätt är via den `search.in` funktion. Om du använder `search.in(Id, 'id1, id2, ...')` i stället för ett uttryck som likhet, kan du räkna med subsekundära svarstider gånger.

Den här artikeln visar hur du utför säkerhetsfiltrering med följande steg:
> [!div class="checklist"]
> * Skapa ett fält som innehåller de huvudsakliga identifierarna 
> * Push- eller uppdatera befintliga dokument med de relevanta huvudnamn identifierarna
> * Utfärda en sökbegäran med `search.in` `filter`

>[!NOTE]
> Hämtningen av huvudkonto-ID: n ingår inte i det här dokumentet. Du bör få det från din identitetsprovider för tjänsten.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search-tjänst](https://docs.microsoft.com/azure/search/search-create-service-portal), och [Azure Search-Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Skapa säkerhet fält

Dina dokument måste innehålla ett fält att ange vilka grupper som har åtkomst. Den här informationen blir filtervillkoren mot vilken dokument har valts eller underkännas resultatuppsättningen som returneras till utfärdaren.
Anta att vi har ett index över skyddade filer och varje fil kan nås av en annan uppsättning användare.
1. Lägg till fält `group_ids` (du kan välja vilket namn här) som en `Collection(Edm.String)`. Kontrollera att fältet har en `filterable` attributet inställt på `true` så att sökresultat filtreras utifrån den åtkomst som användaren har. Exempel: Om du ställer in den `group_ids` fältet `["group_id1, group_id2"]` för dokumentet med `file_name` ”secured_file_b”, endast användare som tillhör gruppen ID: n ”group_id1” eller ”group_id2” har skrivskyddad åtkomst till filen.
   Kontrollera att fältets `retrievable` är attributet `false` så att inte returneras som en del av sökbegäran.
2. Lägg även till `file_id` och `file_name` fält för det här exemplet.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Skicka data till ditt index med hjälp av REST API
  
Utfärda en HTTP POST-begäran till URL-slutpunkt för ditt index. Brödtexten i HTTP-begäran är ett JSON-objekt som innehåller de dokument som ska läggas till:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

I begärandetexten, anger du innehållet i dokument:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Om du vill uppdatera ett befintligt dokument med i listan över grupper kan du använda den `merge` eller `mergeOrUpload` åtgärd:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Mer information om att lägga till eller uppdaterar dokument, kan du läsa [redigera dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Använda säkerhetsfilter

För att trimma dokument baserat på `group_ids` åtkomst, bör du skicka en sökfråga med en `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filter, där ”group_id1 group_id2...” är de grupper som tillhör utfärdaren av Sök-förfrågan.
Det här filtret matchar alla dokument som de `group_ids` fältet innehåller en av de angivna identifierarna.
Fullständig information om sökning dokument med hjälp av Azure Search kan du läsa [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Observera att det här exemplet visar hur du söker efter dokument med hjälp av en POST-begäran.

Skicka HTTP POST-begäran:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Ange filtret i begärandetexten:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Du bör få dokumenten tillbaka där `group_ids` innehåller ”group_id1” eller ”group_id2”. Med andra ord kan du hämta dokument som utfärdaren av förfrågan har läsbehörighet.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Sammanfattning

Detta är hur du kan filtrera resultatet baserat på användarens identitet och Azure Search `search.in()` funktion. Du kan använda den här funktionen för att skicka in princip-ID: n för den begärande användaren att matcha mot huvudnamn identifierare som är associerade med varje måldokument som. När en sökbegäran hanteras den `search.in` funktion som filtrerar ut sökresultat som ingen av användarens huvudnamn har läsbehörighet. Huvudkonto-ID: n kan representera exempelvis säkerhetsgrupper, roller eller även användarens identitet.
 
## <a name="see-also"></a>Se också

+ [Active Directory identity-baserad åtkomstkontroll med hjälp av Azure Search filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Search](search-filters.md)
+ [Säkerhet och åtkomstkontroll i Azure Search-åtgärder](search-security-overview.md)