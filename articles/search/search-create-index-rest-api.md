---
title: Skapa ett index i kod med hjälp av REST-API – Azure Search
description: Skapa ett sökbart fulltextindex i kod med hjälp av HTTP-begäranden och Azure Search REST API.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b4d85d3b8ee7e6a872fdd6bf07917770c4d2ee9e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265268"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Skapa ett Azure Search-index med hjälp av REST-API:et
> [!div class="op_single_selector"]
>
> * [Översikt](search-what-is-an-index.md)
> * [Portalen](search-create-index-portal.md)
> * [NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Den här artikeln beskriver steg för steg hur du skapar ett Azure Search-[index](https://docs.microsoft.com/rest/api/searchservice/Create-Index) med hjälp av REST-API:et för Azure Search.

Innan du följer den här guiden och skapar ett index bör du redan ha [skapat en Azure Search-tjänst](search-create-service-portal.md).

Om du vill skapa ett Azure Search-index med hjälp av REST-API:et skickar du en enkel HTTP POST-begäran till slutpunkten i Azure Search-tjänstens URL. Din indexdefinition finns i begärandetexten i form av välstrukturerat JSON-innehåll.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifiera Azure Search-tjänstens API-administratörsnyckel
Nu när du har etablerat en Azure Search-tjänst kan du skicka HTTP-förfrågningar mot din tjänsts URL-slutpunkt med hjälp av REST-API:et. *Alla* API-förfrågningar måste innehålla API-nyckeln som genererades för Search-tjänsten som du etablerade. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

1. För att hitta din tjänsts API-nycklar måste du logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till Azure Search-tjänstens blad
3. Klicka på nyckelikonen

Tjänsten har *administratörsnycklar* och *frågenycklar*.

* Dina primära och sekundära *administratörsnycklar* ger fullständig behörighet för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor. Det finns två nycklar så att du kan fortsätta att använda den sekundära nyckeln om du bestämmer dig för att återskapa den primära nyckeln och tvärtom.
* Dina *frågenycklar* beviljar läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.

Du kan använda antingen en primär eller sekundär administrationsnyckel när du skapar ett index.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definiera ditt Azure Search-index med välstrukturerad JSON
Ditt index skapas med en enkel HTTP POST-begäran till din tjänst. Brödtexten i HTTP POST-begäran innehåller ett enda JSON-objekt som definierar ditt Azure Search-index.

1. Den första egenskapen för det här JSON-objektet är namnet på ditt index.
2. Den andra egenskapen för JSON-objektet är en JSON-matris med namnet `fields` som innehåller ett separat JSON-objekt för varje fält i indexet. Varje JSON-objekt innehåller flera namn/värde-par för vart och ett av fältattributen, inklusive ”name”, ”type” osv.

Det är viktigt att du har användarupplevelsen och dina affärsbehov i åtanke när du utformar ditt index eftersom varje fält måste tilldelas [rätt egenskaper](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Dessa attribut styr vilka sökfunktioner (filtrering, aspekter, sortering av textsökningar osv) som tillämpas på vilka fält. För attribut som du inte anger aktiveras motsvarande sökfunktion som standard såvida du inte uttryckligen inaktiverar den.

I vårt exempel har vi gett indexet namnet ”hotels” och definierat fälten så här:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Vi har noga valt indexattributen för varje fält baserat på hur vi tror att de kommer att användas i ett program. `hotelId` är exempelvis en unik nyckel som personer som söker efter hotell antagligen inte känner till, så vi inaktiverar fulltextsökning för det fältet genom att ange `searchable` till `false`, vilket sparar utrymme i indexet.

Observera att exakt ett fält i indexet av typen `Edm.String` måste definieras som nyckelfältet.

Indexdefinitionen ovan använder ett språkanalysverktyg för `description_fr`-fältet eftersom det ska lagra fransk text. Mer information om språkanalysverktyg finns i [avsnittet om språkstöd](https://docs.microsoft.com/rest/api/searchservice/Language-support) och i motsvarande [blogginlägg](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="issue-the-http-request"></a>Skicka HTTP-begäran
1. Använd indexdefinitionen som begärandetext och skicka en HTTP POST-begäran till URL:en för Azure Search-tjänstens slutpunkt. I URL:en använder du tjänstnamnet som värdnamn och placerar rätt `api-version` som en frågesträngsparameter (den aktuella API-versionen är `2017-11-11` vid tidpunkten för publiceringen av det här dokumentet).
2. I huvudena för begäran anger du `Content-Type` som `application/json`. Du måste också ange tjänstens administratörsnyckel som du identifierade i steg I i `api-key`-huvudet.

Du måste ange ditt eget tjänstnamn och din egen API-nyckel för att skicka begäran nedan:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


Statuskoden 201 (har skapats) bör returneras om begäran lyckades. Mer information om hur du kan skapa ett index via REST-API:et finns i API-referensen i [API-referensen här](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Mer information om andra HTTP-statuskoder som kan returneras om det uppstår fel finns i [HTTP-statuskoder (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

När du är klar med ett index och vill ta bort det skickar du bara en HTTP DELETE-begäran. Så här skulle vi till exempel göra om vi ville ta bort indexet ”hotels”:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Nästa steg
När du har skapat ett Azure Search-index är det dags att [ladda upp innehållet till indexet](search-what-is-data-import.md) så att du kan börja söka efter data.
