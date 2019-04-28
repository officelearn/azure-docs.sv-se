---
title: Synonymer för frågan utbyggnad med ett sökindex – Azure Search
description: Skapa en synonymmappning för att expandera omfattningen för en sökfråga för ett Azure Search-index. Omfånget är breddade med motsvarande termer som du anger en lista.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4383cc327d8058ca44acd892f41a7a256e3b1727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281810"
---
# <a name="synonyms-in-azure-search"></a>Synonymer i Azure Search

Synonymer i sökmotorer associerar ekvivalenta termer som implicit expanderar omfattningen av en fråga, utan att användaren behöver ange faktiskt termen. Till exempel ska får termen ”hund” och synonymen kopplingar till ”hunddjur” och ”Hundvalp” alla dokument som innehåller ”hund”, ”hunddjur” eller ”Hundvalp” omfattas av frågan.

I Azure Search görs synonymen expansion när en fråga körs. Du kan lägga till synonymmappningar till en tjänst utan några avbrott befintliga åtgärder. Du kan lägga till en **synonymMaps** egenskap till en fältdefinition utan att behöva återskapa indexet.

## <a name="feature-availability"></a>Funktionstillgänglighet

Synonymfunktionen stöds i den senaste api-versionen (api-version = 2017-11-11). Funktionen stöds för närvarande inte på Azure Portal.

## <a name="how-to-use-synonyms-in-azure-search"></a>Hur du använder synonymer i Azure search

I Azure Search utifrån synonymen support synonymmappningar som du definierar och ladda upp till din tjänst. Dessa kartor utgör en oberoende resurs (till exempel index eller datakällor) och kan användas av valfritt sökbart fält i ett index i din söktjänst.

Synonymen mappar och index underhålls separat. När du definierar en synonymmappning och överföra den till din tjänst, kan du aktivera funktionen synonymen på ett fält genom att lägga till en ny egenskap som kallas **synonymMaps** i fältdefinitionen för. Skapa, uppdatera och ta bort en synonymmappning är alltid en åtgärd för hela dokumentet, vilket innebär att du inte kan att skapa, uppdatera eller ta bort delar av synonymmappningen inkrementellt. Uppdaterar även en enda post kräver en inläsning på nytt.

Införliva synonymer i ditt sökprogram är en tvåstegsprocess:

1.  Lägg till en synonymmappning i söktjänsten via API: erna nedan.  

2.  Konfigurera ett sökbart fält så att synonymmappningen används i indexdefinitionen.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps Resource API: er

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Lägga till eller uppdatera en synonymmappning under din tjänst, med hjälp av publicera eller PLACERA.

Synonymmappningar laddas upp till tjänsten via post- eller PUT. Varje regel måste avgränsas med radmatningstecken (”\n”). Du kan definiera upp till 5 000 regler per synonymmappning i en kostnadsfri tjänst och 10 000 regler i alla andra SKU: er. Varje regel kan ha upp till 20 expanderar.

Synonymmappningar måste vara i formatet Apache Solr som beskrivs nedan. Om du har en befintlig synonymen ordlista i olika format och vill använda den direkt, kontakta oss gärna på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Du kan skapa en ny synonymmappning med HTTP POST, som i följande exempel:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Du kan också använda PUT och ange synonymen kartans namn på URI: N. Om mappningen används inte finns skapas den.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr synonymen format

Solr format stöder motsvarande och explicit synonymmappningar. Regler för mappning av uppfylla filterspecifikationen för öppen källkod synonymen av Apache Solr, som beskrivs i det här dokumentet: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Nedan visas en Exempelregel för motsvarande synonymer.
```
USA, United States, United States of America
```

Med regeln ovan är en sökfråga breddas ”Europa” till ”Europa” eller ”USA” eller ”USA”.

Explicit mappning markeras med en pil ”= >”. När du anger en term sekvens med en sökfråga som matchar den vänstra sidan av ”= >” ersätts med varianter på höger sida. Med regeln nedan kan sökfrågor ”Washington”, ”Wash.” eller ”WA” kommer alla skrivas till ”WA”. Explicit mappning bara gäller i den riktning du angav och skriv om inte frågan ”WA” till ”Washington” i det här fallet.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista synonymen mappar under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Få en synonymmappning under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Ta bort en synonymer karta under din tjänst.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfigurera ett sökbart fält så att synonymmappningen används i indexdefinitionen.

En ny fältegenskap **synonymMaps** kan användas för att ange en synonymmappning som ska användas för ett sökbart fält. Synonymmappningar är tjänsten på resurser och kan refereras av varje fält i ett index i tjänsten.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
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

**synonymMaps** kan anges för sökbara fält av typen 'Edm.String' eller 'Collection(Edm.String)'.

> [!NOTE]
> Du kan bara ha en synonym mappa per fält. Om du vill använda flera synonymmappningar, kontakta oss gärna på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Effekten av synonymer i andra sökfunktioner

Synonymfunktionen skriver om den ursprungliga frågan med synonymer med OR-operator. Därför behandlar träffar syntaxmarkering och poängprofiler du den ursprungliga perioden och synonymer som likvärdiga.

Synonymen funktionen gäller för sökfrågor och gäller inte för filter eller fasetter. På samma sätt baseras förslag endast på den ursprungliga sikt. synonymen matchningar visas inte i svaret.

Synonymen expanderar gäller inte för jokertecken söktermer; prefix, fuzzy, och regex villkor inte är expanderat.

Om du behöver göra en enda fråga som gäller synonymen expansion och jokertecken, regex eller fuzzy sökningar, kan du kombinera frågor med OR-syntax. Till exempel om du vill kombinera synonymer med jokertecken för enkel frågesyntax termen skulle vara `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Tips för att skapa en synonymmappning

- En kortfattad, väl utformad synonymmappning är effektivare än en fullständig lista över möjliga matchningar. Alltför stora och komplexa ordlistor ta längre tid att parsa och påverka svarstiden för fråga om frågan expanderar till många synonymer. I stället för att gissa då villkoren kan användas, får de faktiska villkor via en [Sök trafik analysrapporten](search-traffic-analytics.md).

- Som både en preliminär och validering utöva, aktivera och sedan använda rapporten för att exakt avgöra vilka villkor ska dra nytta av en synonym matchning och sedan fortsätta att använda den som verifieringen att din synonymmappning ger ett bättre resultat. I rapporten fördefinierade ger paneler ”de vanligaste sökfrågorna” och ”noll resultatet sökfrågor” dig informationen som krävs.

- Du kan skapa flera synonymmappningar för search-program (till exempel efter språk om ditt program har stöd för en flerspråkig kundbas). Ett fält kan för närvarande kan bara använda en av dem. Du kan uppdatera synonymMaps-egenskapen för ett fält när som helst.

## <a name="next-steps"></a>Nästa steg

- Om du har ett befintligt index i en utvecklingsmiljö (icke-produktion) kan du experimentera med en liten ordlista träffmarkering om du vill se hur att lägga till synonymer sökupplevelse, inklusive påverkan på poängprofiler och förslag.

- [Aktivera söktrafikanalys](search-traffic-analytics.md) och Använd fördefinierade Power BI-rapporten för att lära dig vilka ord som används mest och vilka som returnerar noll dokument. Som enda verktyg med de här insikterna, ändra ordlista med synonymer för improduktiva frågor som ska matcha mot dokument i indexet.
