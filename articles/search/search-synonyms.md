---
title: Synonymer för frågans expansion över ett Sök index
titleSuffix: Azure Cognitive Search
description: Skapa en synonym karta för att expandera omfånget för en Sök fråga på ett Azure Kognitiv sökning-index. Omfattningen utökas för att inkludera motsvarande termer som du anger i en lista.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a8f1fa07b94072d37cf83320b6c8956d3b412f12
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701124"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonymer i Azure Kognitiv sökning

Synonymer i sökmotorer associerar likvärdiga villkor som implicit expanderar omfånget för en fråga utan att användaren behöver ange termen. Till exempel, med tanke på termen "hund" och synonym associationer för "Canine" och "Puppy", kommer alla dokument som innehåller "hund", "Canine" eller "Puppy" att falla inom frågans omfång.

I Azure Kognitiv sökning görs synonym expansion vid tidpunkten för frågan. Du kan lägga till synonym Maps till en tjänst utan avbrott i befintliga åtgärder. Du kan lägga till en  **synonymMaps** -egenskap i en fält definition utan att behöva bygga om indexet.

## <a name="create-synonyms"></a>Skapa synonymer

Det finns inget Portal stöd för att skapa synonymer, men du kan använda REST API eller .NET SDK. För att komma igång med REST rekommenderar vi [Postman eller Visual Studio Code](search-get-started-rest.md) och formulering av förfrågningar som använder detta API: [skapa synonym Maps](/rest/api/searchservice/create-synonym-map). För C#-utvecklare kan du komma igång med att [lägga till synonymer i Azure kognitiv sökning med C#](search-synonyms-tutorial-sdk.md).

Om du använder [Kundhanterade nycklar](search-security-manage-encryption-keys.md) för tjänstens kryptering på plats kan du tillämpa det skyddet på innehållet i synonym kartan.

## <a name="use-synonyms"></a>Använd synonymer

I Azure Kognitiv sökning baseras synonym stödet på synonym Maps som du definierar och överför till din tjänst. Dessa kartor utgör en oberoende resurs (t. ex. index eller data källor) och kan användas av valfritt sökbart fält i alla index i Sök tjänsten.

Synonym Maps och index upprätthålls oberoende av varandra. När du har definierat en synonym mappning och laddat upp den till tjänsten kan du aktivera synonym funktionen i ett fält genom att lägga till en ny egenskap med namnet **synonymMaps** i fält definitionen. Att skapa, uppdatera och ta bort en synonym mappning är alltid en hel dokument åtgärd, vilket innebär att du inte kan skapa, uppdatera eller ta bort delar av synonym kartan stegvis. Det krävs en ombelastning för att uppdatera även en enskild post.

Att införliva synonymer i sökprogrammet är en två stegs process:

1.  Lägg till en synonym mappning till din Sök tjänst via API: erna nedan.  

2.  Konfigurera ett sökbart fält att använda synonym mappningen i index definitionen.

Du kan skapa flera synonym mappningar för ditt sökprogram (till exempel efter språk om ditt program stöder en flerspråkig kund bas). För närvarande kan ett fält endast använda en av dem. Du kan när som helst uppdatera ett fälts synonymMaps-egenskap.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps-resurs-API: er

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Lägg till eller uppdatera en synonym avbildning under din tjänst med POST eller placering.

Synonym Maps överförs till tjänsten via POST eller placering. Varje regel måste avgränsas med det nya rad symbolen (\n). Du kan definiera upp till 5 000 regler per synonym mappning i en kostnads fri tjänst och 20 000 regler per karta i alla andra SKU: er. Varje regel kan ha upp till 20 expansionar.

Synonym Maps måste vara i formatet Apache Solr som beskrivs nedan. Om du har en befintlig synonym ord lista i ett annat format och vill använda den direkt, kan du berätta för oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Du kan skapa en ny synonym mappning med HTTP POST, som i följande exempel:

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

Du kan också använda Lägg till och ange synonym mappnings namnet på URI: n. Om synonym mappningen inte finns kommer den att skapas.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Formatet Apache Solr synonym

Solr-formatet stöder motsvarande och explicita synonym mappningar. Mappnings reglerna följer synonym filter specifikationen med öppen källkod för Apache Solr, som beskrivs i det här dokumentet: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Nedan visas en exempel regel för motsvarande synonymer.

```
USA, United States, United States of America
```

Med regeln ovan expanderas en Sök fråga "USA" till "USA" eller "USA" eller "USA av Amerika".

Explicit mappning betecknas av en pil "=>". När det här alternativet har angetts ersätts en term ordning i en Sök fråga som matchar den vänstra sidan av "=>" med alternativen på den högra sidan. Med den här regeln nedan, Sök efter frågor "Washington", "tvätt". eller "WA" kommer att skrivas om till "WA". Explicit mappning gäller endast i den angivna riktningen och skriver inte om frågan "WA" till "Washington" i det här fallet.

```
Washington, Wash., WA => WA
```

Om du behöver definiera synonymer som innehåller kommatecken kan du kringgå dem med ett omvänt snedstreck, som i det här exemplet:

```
WA\, USA, WA, Washington
```

Eftersom omvänt snedstreck själva är ett specialtecken i andra språk som JSON och C#, behöver du förmodligen dubbla escape-tecken. Till exempel skulle JSON som skickas till REST API för ovanstående synonym karta se ut så här:

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>Visa en lista över synonym mappningar under din tjänst.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>Få en synonym karta under din tjänst.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>Ta bort en synonym mapp under din tjänst.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfigurera ett sökbart fält att använda synonym mappningen i index definitionen.

En ny fält egenskap **synonymMaps** kan användas för att ange en synonym mappning som ska användas för ett sökbart fält. Synonym Maps är service nivå resurser och kan refereras till av fält i ett index under tjänsten.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
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
```

**synonymMaps** kan anges för sökbara fält av typen ' EDM. String ' eller ' Collection (EDM. String) '.

> [!NOTE]
> Du kan bara ha en synonym mappning per fält. Om du vill använda flera synonyma kartor kan du berätta för oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Effekten av synonymer på andra Sök funktioner

Funktionen synonymer skriver om den ursprungliga frågan med synonymer med operatorn OR. Av den anledningen kan träff markeringar och bedömnings profiler behandla den ursprungliga termen och synonymer som likvärdiga.

Synonym-funktionen gäller för Sök frågor och gäller inte för filter eller FACET. På samma sätt baseras förslag endast på den ursprungliga termen. synonym matchningar visas inte i svaret.

Synonym expansionar gäller inte för sökord med jokertecken. termerna prefix, fuzzy och regex expanderas inte.

Om du behöver göra en enskild fråga som använder synonym expansion och jokertecken, regex eller fuzzy-sökningar kan du kombinera frågorna med hjälp av eller-syntaxen. Om du t. ex. vill kombinera synonymer med jokertecken för enkel frågesyntax skulle termen vara `<query> | <query>*` .

Om du har ett befintligt index i en utvecklings miljö (icke-produktion) kan du experimentera med en liten ord lista för att se hur tillägget av synonymer ändrar Sök upplevelsen, inklusive påverkan på bedömnings profiler, träff markering och förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en synonym karta](/rest/api/searchservice/create-synonym-map)