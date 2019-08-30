---
title: Synonymer för frågans expansion över ett Sök index – Azure Search
description: Skapa en synonym karta för att expandera omfånget för en Sök fråga på ett Azure Search index. Omfattningen utökas för att inkludera motsvarande termer som du anger i en lista.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
manager: nitinme
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d9ddb5af42c538558a69ce68e7ea90161c947b12
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186455"
---
# <a name="synonyms-in-azure-search"></a>Synonymer i Azure Search

Synonymer i sökmotorer associerar likvärdiga villkor som implicit expanderar omfånget för en fråga utan att användaren behöver ange termen. Till exempel, med tanke på termen "hund" och synonym associationer för "Canine" och "Puppy", kommer alla dokument som innehåller "hund", "Canine" eller "Puppy" att falla inom frågans omfång.

I Azure Search görs synonym expansion vid tidpunkten för frågan. Du kan lägga till synonym Maps till en tjänst utan avbrott i befintliga åtgärder. Du kan lägga till en **synonymMaps** -egenskap i en fält definition utan att behöva bygga om indexet.

## <a name="create-synonyms"></a>Skapa synonymer

Det finns inget Portal stöd för att skapa synonymer, men du kan använda REST API eller .NET SDK. För att komma igång med REST rekommenderar vi att du [använder Postman](search-get-started-postman.md) och formulering av begär Anden med följande API: [Skapa synonym Maps](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). För C# utvecklare kan du komma igång med att [lägga till synonymer i Azure- C#sökning med ](search-synonyms-tutorial-sdk.md).

Om du använder Kundhanterade [nycklar](search-security-manage-encryption-keys.md) för tjänstens kryptering på plats kan du tillämpa det skyddet på innehållet i synonym kartan.

## <a name="use-synonyms"></a>Använda synonymer

I Azure Search baseras synonym stödet på synonym Maps som du definierar och överför till din tjänst. Dessa kartor utgör en oberoende resurs (t. ex. index eller data källor) och kan användas av valfritt sökbart fält i alla index i Sök tjänsten.

Synonym Maps och index upprätthålls oberoende av varandra. När du har definierat en synonym mappning och laddat upp den till tjänsten kan du aktivera synonym funktionen i ett fält genom att lägga till en ny egenskap med namnet **synonymMaps** i fält definitionen. Att skapa, uppdatera och ta bort en synonym mappning är alltid en hel dokument åtgärd, vilket innebär att du inte kan skapa, uppdatera eller ta bort delar av synonym kartan stegvis. Det krävs en ombelastning för att uppdatera även en enskild post.

Att införliva synonymer i sökprogrammet är en två stegs process:

1.  Lägg till en synonym mappning till din Sök tjänst via API: erna nedan.  

2.  Konfigurera ett sökbart fält att använda synonym mappningen i index definitionen.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps-resurs-API: er

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Lägg till eller uppdatera en synonym avbildning under din tjänst med POST eller placering.

Synonym Maps överförs till tjänsten via POST eller placering. Varje regel måste avgränsas med det nya rad symbolen (\n). Du kan definiera upp till 5 000 regler per synonym mappning i en kostnads fri tjänst och 10 000 regler i alla andra SKU: er. Varje regel kan ha upp till 20 expansionar.

Synonym Maps måste vara i formatet Apache Solr som beskrivs nedan. Om du har en befintlig synonym ord lista i ett annat format och vill använda den direkt, kan du berätta för oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Du kan skapa en ny synonym mappning med HTTP POST, som i följande exempel:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Du kan också använda Lägg till och ange synonym mappnings namnet på URI: n. Om synonym mappningen inte finns kommer den att skapas.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formatet Apache Solr synonym

Solr-formatet stöder motsvarande och explicita synonym mappningar. Mappnings regler följer synonym filter specifikationen med öppen källkod för Apache Solr, som beskrivs i det här dokumentet: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Nedan visas en exempel regel för motsvarande synonymer.
```
USA, United States, United States of America
```

Med regeln ovan expanderas en Sök fråga "USA" till "USA" eller "USA" eller "USA av Amerika".

Explicit mappning betecknas av en pil "= >". När det här alternativet har angetts ersätts en term ordning i en Sök fråga som matchar den vänstra sidan av "= >" med alternativen på den högra sidan. Med den här regeln nedan, Sök efter frågor "Washington", "tvätt". eller "WA" kommer att skrivas om till "WA". Explicit mappning gäller endast i den angivna riktningen och skriver inte om frågan "WA" till "Washington" i det här fallet.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Visa en lista över synonym mappningar under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Få en synonym karta under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Ta bort en synonym mapp under din tjänst.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfigurera ett sökbart fält att använda synonym mappningen i index definitionen.

En ny fält egenskap **synonymMaps** kan användas för att ange en synonym mappning som ska användas för ett sökbart fält. Synonym Maps är service nivå resurser och kan refereras till av fält i ett index under tjänsten.

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

**synonymMaps** kan anges för sökbara fält av typen ' EDM. String ' eller ' Collection (EDM. String) '.

> [!NOTE]
> Du kan bara ha en synonym mappning per fält. Om du vill använda flera synonyma kartor kan du berätta för oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Effekten av synonymer på andra Sök funktioner

Funktionen synonymer skriver om den ursprungliga frågan med synonymer med operatorn OR. Av den anledningen kan träff markeringar och bedömnings profiler behandla den ursprungliga termen och synonymer som likvärdiga.

Synonym-funktionen gäller för Sök frågor och gäller inte för filter eller FACET. På samma sätt baseras förslag endast på den ursprungliga termen. synonym matchningar visas inte i svaret.

Synonym expansionar gäller inte för sökord med jokertecken. termerna prefix, fuzzy och regex expanderas inte.

Om du behöver göra en enskild fråga som använder synonym expansion och jokertecken, regex eller fuzzy-sökningar kan du kombinera frågorna med hjälp av eller-syntaxen. Om du t. ex. vill kombinera synonymer med jokertecken för enkel frågesyntax skulle termen vara `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Tips för att skapa en synonym karta

- En kortfattad och väldesignad synonym karta är mer effektiv än en fullständig lista över möjliga matchningar. Alltför stora eller komplexa ord listor tar längre tid att parsa och påverka svars tiden om frågan expanderar till många synonymer. I stället för att gissa dig över vilka villkor som kan användas kan du hämta de faktiska villkoren via en [analys rapport för Sök trafik](search-traffic-analytics.md).

- Som både en preliminär och validerings övning kan du aktivera och sedan använda den här rapporten för att exakt avgöra vilka villkor som kommer att ha nytta av en synonym matchning, och sedan fortsätta att använda den som en verifiering av att synonym kartan ger ett bättre resultat. I den fördefinierade rapporten ger panelerna "de vanligaste Sök frågorna" och "noll-result Sök frågor" den information som krävs.

- Du kan skapa flera synonym mappningar för ditt sökprogram (till exempel efter språk om ditt program stöder en flerspråkig kund bas). För närvarande kan ett fält endast använda en av dem. Du kan när som helst uppdatera ett fälts synonymMaps-egenskap.

## <a name="next-steps"></a>Nästa steg

- Om du har ett befintligt index i en utvecklings miljö (icke-produktion) kan du experimentera med en liten ord lista för att se hur tillägget av synonymer ändrar Sök upplevelsen, inklusive påverkan på bedömnings profiler, träff markering och förslag.

- [Aktivera Sök trafik analys](search-traffic-analytics.md) och Använd den fördefinierade Power BI rapporten för att lära dig vilka termer som används mest och vilka som returnerar noll dokument. Väpnade med dessa insikter, ändra ord listan så att den innehåller synonymer för att få till exempel frågor som ska lösas till dokument i ditt index.
