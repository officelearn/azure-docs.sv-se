---
title: Säkerhetsfilter trimning resultat i Azure Search | Microsoft Docs
description: Åtkomstkontroll på Azure Search-innehåll med hjälp av säkerhetsfilter och användaridentiteter.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: dd26676b74431566b3631b8a79cd06bcf3022518
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792808"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Säkerhetsfilter trimning resultat i Azure Search

Du kan använda säkerhetsfilter Beskär sökresultat i Azure Search baserat på användarens identitet. Den här sökinställningar kräver vanligtvis jämföra identiteten hos den som begär sökningen mot ett fält som innehåller de principer som har behörighet till dokumentet. När en matchning hittas, har användaren eller säkerhetsobjekt (till exempel en grupp eller roll) åtkomst till dokumentet.

Ett sätt att uppnå säkerhet filtrering är via en komplicerad disjunktion på likheten uttryck: till exempel `Id eq 'id1' or Id eq 'id2'`, och så vidare. Den här metoden är problematiskt, svårt att underhålla, och i fall där listan innehåller hundratals eller tusentals värden långsammare svarstid för frågor med många sekunder. 

En enklare och snabbare metod är via den `search.in` funktion. Om du använder `search.in(Id, 'id1, id2, ...')` i stället för ett lika uttryck, du kan förvänta dig sekund svar gånger.

Den här artikeln visar hur du utför säkerhetsfiltrering med följande steg:
> [!div class="checklist"]
> * Skapa ett fält som innehåller de huvudsakliga identifierarna 
> * Push- eller uppdatera befintliga dokument med de relevanta huvudnamn identifierarna
> * Utfärda en sökbegäran med `search.in` `filter`

>[!NOTE]
> Hämtningen av de huvudsakliga identifierarna ingår inte i det här dokumentet. Du bör få det från leverantören identitet.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search-tjänsten](https://docs.microsoft.com/azure/search/search-create-service-portal), och [Azure Search Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Skapa säkerhet fält

Dina dokument måste innehålla ett fält att ange vilka grupper som har åtkomst. Den här informationen blir filtervillkoren mot vilken dokument har valts eller avvisas från resultatuppsättningen som returneras till utfärdaren.
Anta att vi har ett index över skyddade filer och varje fil kan komma åt en annan uppsättning användare.
1. Lägg till fältet `group_ids` (du kan välja alla namn) som en `Collection(Edm.String)`. Kontrollera att fältet har en `filterable` -attributet inställt på `true` så att sökresultat filtreras utifrån den åtkomst som användaren har. Till exempel om du ställer in den `group_ids` till `["group_id1, group_id2"]` för dokumentet med `file_name` ”secured_file_b”, endast användare som hör till grupp-ID: n ”group_id1” eller ”group_id2” har skrivskyddad åtkomst till filen.
   Kontrollera att fältet `retrievable` -attributet är inställt på `false` så att den inte returneras som en del av sökbegäran.
2. Lägg även till `file_id` och `file_name` fälten för det här exemplet.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Skicka data till ditt index med hjälp av REST-API
  
Utfärda en HTTP POST-begäran till ditt index URL-slutpunkt. Innehållet i HTTP-begäran är en JSON-objekt som innehåller de dokument som ska läggas till:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

Ange innehållet i dokumenten i frågans brödtext:

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

Om du behöver uppdatera ett befintligt dokument med listan över grupper som du kan använda den `merge` eller `mergeOrUpload` åtgärd:

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

Mer information om att lägga till eller uppdatera dokument, kan du läsa [redigera dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Tillämpa säkerhetsfiltret

För att kunna ta bort dokument baserat på `group_ids` åtkomst, ska du utfärda en sökfråga med en `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filter, där 'group_id1 group_id2... ”de grupper som sökningen begäran utfärdaren tillhör.
Det här filtret matchar alla dokument som den `group_ids` fältet innehåller en av de angivna identifierarna.
Fullständig information om sökning dokument med Azure Search kan du läsa [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Observera att det här exemplet visas hur du söker dokument med hjälp av en POST-begäran.

Skicka en HTTP POST-begäran:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Ange filtret i frågans brödtext:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Du bör få dokumenten tillbaka där `group_ids` innehåller ”group_id1” eller ”group_id2”. Med andra ord kan du hämta dokument som begäran utfärdaren har läsbehörighet.

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

Detta är hur du kan filtrera resultatet baserat på användarens identitet och Azure Search `search.in()` funktion. Du kan använda den här funktionen för att skicka in huvudnamn identifierare för den begärande användaren för matchning mot huvudnamn identifierare som är kopplade till varje måldokument som. När en sökbegäran hanteras den `search.in` funktionen filtreras sökresultatet för vilka ingen av användarens säkerhetsobjekt har läsbehörighet. De huvudsakliga identifierarna kan representera säkerhetsgrupper, roller eller även användarens identitet.
 
## <a name="see-also"></a>Se också

+ [Active Directory identitetsbaserade åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Search](search-filters.md)
+ [Data säkerhet och åtkomstkontroll i Azure-sökningar](search-security-overview.md)