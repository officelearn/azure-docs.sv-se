---
title: Synonymer för frågeexpansion över ett sökindex
titleSuffix: Azure Cognitive Search
description: Skapa en synonymkarta för att utöka omfattningen av en sökfråga på ett Azure Cognitive Search-index. Scopet breddas så att det omfattar likvärdiga termer som du anger i en lista.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194350"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonymer i Azure Cognitive Search

Synonymer i sökmotorer associerar motsvarande termer som underförstått utökar frågans omfattning, utan att användaren faktiskt behöver ange termen. Till exempel, med tanke på termen "hund" och synonymasociationer av "hund" och "valp", alla dokument som innehåller "hund", "hund" eller "valp" kommer att falla inom ramen för frågan.

I Azure Cognitive Search utförs synonymexpansion vid frågetid. Du kan lägga till synonymmappningar i en tjänst utan avbrott i befintliga åtgärder. Du kan lägga till en **synonymMaps-egenskap** i en fältdefinition utan att behöva återskapa indexet.

## <a name="create-synonyms"></a>Skapa synonymer

Det finns inget portalstöd för att skapa synonymer, men du kan använda REST API eller .NET SDK. För att komma igång med REST rekommenderar vi att [du använder Postman](search-get-started-postman.md) och formulering av begäranden med hjälp av det här API:et: [Skapa synonymkartor](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). För C#-utvecklare kan du komma igång med [Lägg till synonymer i Azure Cognitive Searching med C#](search-synonyms-tutorial-sdk.md).

Om du använder [kundhanterade nycklar](search-security-manage-encryption-keys.md) för kryptering på vilosidan på tjänstsidan kan du använda det skyddet på innehållet i synonymkartan.

## <a name="use-synonyms"></a>Använd synonymer

I Azure Cognitive Search baseras synonymstöd på synonymmappningar som du definierar och överför till din tjänst. Dessa kartor utgör en oberoende resurs (som index eller datakällor) och kan användas av valbara fält i alla index i söktjänsten.

Synonymen kartlägger och index underhålls självständigt. När du har definierat en synonymkarta och överför den till tjänsten kan du aktivera synonymfunktionen i ett fält genom att lägga till en ny egenskap som kallas **synonymMappar** i fältdefinitionen. Skapa, uppdatera och ta bort en synonymkarta är alltid en heldokumentåtgärd, vilket innebär att du inte kan skapa, uppdatera eller ta bort delar av synonymmappningen stegvis. För att uppdatera även en enda post krävs en omladdning.

Att införliva synonymer i sökprogrammet är en tvåstegsprocess:

1.  Lägg till en synonymkarta i söktjänsten via API:erna nedan.  

2.  Konfigurera ett sökbart fält så att synonymmappningen används i indexdefinitionen.

Du kan skapa flera synonymkartor för ditt sökprogram (till exempel efter språk om ditt program stöder en flerspråkig kundbas). För närvarande kan ett fält bara använda ett av dem. Du kan uppdatera egenskapen synonymMaps när som helst i ett fält.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps-resurs-API:er

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Lägg till eller uppdatera en synonymkarta under din tjänst med hjälp av POST eller PUT.

Synonymkartor laddas upp till tjänsten via POST eller PUT. Varje regel måste avgränsas med det nya radtecknet (\n'). Du kan definiera upp till 5 000 regler per synonymkarta i en kostnadsfri tjänst och 20 000 regler per karta i alla andra SKU:er. Varje regel kan ha upp till 20 expansioner.

Synonym kartor måste vara i Apache Solr format som förklaras nedan. Om du har en befintlig synonymordlista i ett annat format och vill använda den direkt, vänligen meddela oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Du kan skapa en ny synonymkarta med HTTP POST, som i följande exempel:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Du kan också använda PUT och ange synonymmappningsnamnet på URI.Alternatively, you can use PUT and specify the synonym map name on the URI. Om synonymkartan inte finns skapas den.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Synonymformat för Apache Solr

Solr-formatet stöder likvärdiga och explicita synonymmappningar. Mappningsregler följer synonymfiltret för öppen källkod för Apache Solr, som beskrivs i det här dokumentet: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Nedan följer en exempelregel för motsvarande synonymer.
```
USA, United States, United States of America
```

Med regeln ovan, en sökfråga "USA" kommer att expandera till "USA" ELLER "USA" ELLER "USA".

Explicit mappning betecknas med en pil "=>". När det anges ersätts en termsekvens för en sökfråga som matchar den vänstra sidan av "=>" med alternativen till höger. Med tanke på regeln nedan, sökfrågor "Washington", "Tvätta.", eller "WA" kommer alla att skrivas om till "WA". Explicit mappning gäller endast i den angivna riktningen och skriver inte om frågan "WA" till "Washington" i det här fallet.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista synonymkartor under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Få en synonymkarta under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Ta bort en synonymkarta under din tjänst.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfigurera ett sökbart fält så att synonymmappningen används i indexdefinitionen.

En ny fältegenskap **synonymMappar** kan användas för att ange en synonymkarta som ska användas för ett sökbart fält. Synonymkartor är servicenivåresurser och kan refereras av alla fält i ett index under tjänsten.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMappar** kan anges för sökbara fält av typen "Edm.String" eller "Collection(Edm.String)".

> [!NOTE]
> Du kan bara ha en synonymkarta per fält. Om du vill använda flera synonym kartor, vänligen meddela oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Synonymernas inverkan på andra sökfunktioner

Synonymfunktionen skriver om den ursprungliga frågan med synonymer med operatorn ELLER. Av denna anledning, hit markering och scoring profiler behandla den ursprungliga termen och synonymer som likvärdiga.

Synonymfunktionen gäller för sökfrågor och gäller inte filter eller faset. På samma sätt baseras förslagen endast på den ursprungliga termen. synonymmatchningar visas inte i svaret.

Synonymexpansioner gäller inte för jokerteckensöktermer. prefix- och otydliga och regex-termer expanderas inte.

Om du behöver göra en enda fråga som tillämpar synonymexpansion och jokertecken, regex eller fuzzy sökningar, kan du kombinera frågorna med hjälp av ELLER syntax. Om du till exempel vill kombinera synonymer med jokertecken `<query> | <query>*`för enkel frågesyntax skulle termen vara .

Om du har ett befintligt index i en utvecklingsmiljö (icke-produktion) kan du experimentera med en liten ordlista för att se hur tillägg av synonymer ändrar sökupplevelsen, inklusive påverkan på bedömningsprofiler, träffmarkering och förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en synonymkarta](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)