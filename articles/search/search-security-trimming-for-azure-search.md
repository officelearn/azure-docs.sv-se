---
title: Säkerhetsfilter för trimning av resultat
titleSuffix: Azure Cognitive Search
description: Åtkomstkontroll för Azure Cognitive Search-innehåll med hjälp av säkerhetsfilter och användaridentiteter.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794266"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Säkerhetsfilter för trimning av resultat i Azure Cognitive Search

Du kan använda säkerhetsfilter för att trimma sökresultat i Azure Cognitive Search baserat på användaridentitet. Den här sökupplevelsen kräver i allmänhet att man jämför identiteten för den som begär sökningen mot ett fält som innehåller de principer som har behörighet i dokumentet. När en matchning hittas har användaren eller huvudnumret (till exempel en grupp eller roll) åtkomst till dokumentet.

Ett sätt att uppnå säkerhetsfiltrering är genom en komplicerad disjunction av jämställdhetsuttryck: till exempel `Id eq 'id1' or Id eq 'id2'`, och så vidare. Den här metoden är felbenägen, svår att underhålla och i de fall där listan innehåller hundratals eller tusentals värden, saktar ner frågesvarstiden med många sekunder. 

Ett enklare och snabbare `search.in` tillvägagångssätt är genom funktionen. Om du `search.in(Id, 'id1, id2, ...')` använder i stället för ett likhetsuttryck kan du förvänta dig svarstider under sekunden.

I den här artikeln visas hur du utför säkerhetsfiltrering med hjälp av följande steg:
> [!div class="checklist"]
> * Skapa ett fält som innehåller huvudidentifierare 
> * Driva eller uppdatera befintliga dokument med relevanta huvudidentifierare
> * Utfärda en sökbegäran med `search.in``filter`

>[!NOTE]
> Processen att hämta huvudidentifierarna beskrivs inte i det här dokumentet. Du bör få det från din identitetstjänstleverantör.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en [Azure-prenumeration,](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) [Azure Cognitive Search-tjänst](https://docs.microsoft.com/azure/search/search-create-service-portal)och [Azure Cognitive Search Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Skapa säkerhetsfält

Dina dokument måste innehålla ett fält som anger vilka grupper som har åtkomst. Den här informationen blir de filtervillkor mot vilka dokument väljs eller avvisas från resultatuppsättningen som returneras till utfärdaren.
Låt oss anta att vi har ett index över säkra filer, och varje fil är tillgänglig för en annan uppsättning användare.
1. Lägg `group_ids` till fält (du kan välja `Collection(Edm.String)`vilket namn som helst här) som ett . Kontrollera att fältet `filterable` har ett `true` attribut inställt på så att sökresultaten filtreras baserat på den åtkomst som användaren har. Om du till exempel `group_ids` anger `["group_id1, group_id2"]` fältet till `file_name` för dokumentet med "secured_file_b", har endast användare som tillhör grupp-ID :group_id1" eller "group_id2" läsbehörighet till filen.
   Kontrollera att fältets `retrievable` attribut är `false` inställt på så att det inte returneras som en del av sökbegäran.
2. Lägg `file_id` också `file_name` till och fält för det här exemplets skull.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Att trycka in data i ditt index med REST API
  
Utfärda en HTTP POST-begäran till indexets URL-slutpunkt. Brödtexten för HTTP-begäran är ett JSON-objekt som innehåller de dokument som ska läggas till:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

I brödtexten för begäran anger du innehållet i dina dokument:

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

Om du behöver uppdatera ett befintligt dokument med listan `merge` `mergeOrUpload` över grupper kan du använda åtgärden eller:

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

Fullständig information om hur du lägger till eller uppdaterar dokument kan du läsa [Redigera dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Använda säkerhetsfiltret

För att trimma dokument `group_ids` baserat på åtkomst bör du `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` utfärda en sökfråga med ett filter, där "group_id1, group_id2,..." är de grupper som utfärdaren för sökbegäran tillhör.
Det här filtret matchar `group_ids` alla dokument för vilka fältet innehåller en av de angivna identifierarna.
Fullständig information om hur du söker efter dokument med Hjälp av Azure Cognitive Search kan du läsa [Sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Observera att det här exemplet visar hur du söker efter dokument med hjälp av en POST-begäran.

Utfärda HTTP POST-begäran:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Ange filtret i begäranden:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Du bör få dokumenten tillbaka där `group_ids` innehåller antingen "group_id1" eller "group_id2". Med andra ord får du de dokument som utfärdaren för begäran har läsbehörighet till.

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
## <a name="conclusion"></a>Slutsats

Så här kan du filtrera resultat baserat på `search.in()` användaridentitet och Azure Cognitive Search-funktion. Du kan använda den här funktionen för att skicka principidentifierare för den begärande användaren att matcha mot huvudidentifierare som är associerade med varje måldokument. När en sökbegäran hanteras filtrerar `search.in` funktionen bort sökresultat som ingen av användarens huvudnamn har läsbehörighet för. Huvudidentifierarna kan representera saker som säkerhetsgrupper, roller eller till och med användarens egen identitet.
 
## <a name="see-also"></a>Se även

+ [Active Directory-identitetsbaserad åtkomstkontroll med hjälp av Azure Cognitive Search-filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Cognitive Search](search-filters.md)
+ [Datasäkerhet och åtkomstkontroll i Azure Cognitive Search-åtgärder](search-security-overview.md)