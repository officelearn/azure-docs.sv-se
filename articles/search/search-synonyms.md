---
pageTitle: Synonyms in Azure Search (preview) | Microsoft Docs
description: "Preliminär dokumentation för funktionen synonymer (förhandsgranskning), visas i Azure Search REST API."
services: search
documentationCenter: 
authors: mhko
manager: pablocas
editor: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/07/2016
ms.author: nateko
ms.openlocfilehash: 447abc48cca3dee398e641f8458e52a5b2cb8e42
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="synonyms-in-azure-search-preview"></a>Synonymer i Azure Search (förhandsgranskning)

Synonymer i sökmotorer associera motsvarande termer som implicit expanderar omfattningen av en fråga, utan att användaren behöver ange faktiskt termen. Till exempel ska angivna termen ”hund” och synonymen kopplingarna ”hunddjur” och ”Hundvalp” alla dokument som innehåller ”hund”, ”hunddjur” eller ”Hundvalp” omfattas av frågan.

I Azure Search görs synonymen expansion när databasfrågan. Du kan lägga till synonymen mappar till en tjänst utan några avbrott befintliga drift. Du kan lägga till en **synonymMaps** egenskapen till en fältdefinition av utan att behöva återskapa indexet.

## <a name="feature-availability"></a>Funktionstillgänglighet

Funktionen synonymer är för närvarande under förhandsgranskning och stöds bara i den senaste api-förhandsversionen (api-version = 2016-09-01-Preview). Funktionen stöds för närvarande inte på Azure Portal. Eftersom API-version anges i begäran, är det möjligt att kombinera allmänt tillgänglig (GA) och förhandsgranska API: er i samma app. Dock kan preview API: er inte är under SLA och funktioner ändras, så vi inte rekommenderar att använda dem i program i produktion.

## <a name="how-to-use-synonyms-in-azure-search"></a>Hur du använder synonymer i Azure search

I Azure Search baseras synonymen stöd på synonymen mappningar som du definierar och ladda upp till din tjänst. Dessa mappningar utgör en oberoende resurs (till exempel index eller datakällor) och kan användas av alla sökbara fält i ett index i din söktjänst.

Synonymen mappar och index hanteras oberoende av varandra. När du definierar en synonym karta och överföra den till din tjänst kan du aktivera funktionen synonymen på ett fält genom att lägga till den nya egenskapen **synonymMaps** i fältdefinitionen. Skapa, uppdatera och ta bort en synonym karta är alltid en åtgärd för hela dokumentet, vilket innebär att du inte går att skapa, uppdatera eller ta bort delar av synonymen kartan inkrementellt. Uppdatering av även en enda post kräver en Läs in igen.

Integrera synonymer i tillämpningsprogrammet search är en tvåstegsprocess:

1.  Lägga till en synonym karta till din söktjänst via API: er nedan.  

2.  Konfigurera ett sökbara fält om du vill använda synonymen kartan i indexdefinitionen.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps Resource API: er

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Lägga till eller uppdatera en synonym karta under din tjänst, med hjälp av bokför eller PLACERA.

Synonymen maps överförs till tjänsten via POST eller PUT. Varje regel måste avgränsas med ett tecken för ny rad (\n). Du kan ange upp till 5 000 regler per synonymen kartan i en kostnadsfri tjänst och 10 000 regler i alla andra SKU: er. Varje regel kan ha upp till 20 expansion.

I den här förhandsgranskningen måste synonymen mappningar vara i formatet Apache Solr som förklaras nedan. Om du har en befintlig synonymen ordlista i ett annat format och vill använda den direkt, meddela oss på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Du kan skapa en ny synonymen karta med hjälp av HTTP POST, som i följande exempel:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Du kan också använda PUT och ange mappningsnamn synonymen på URI: N. Om synonymen mappningen inte finns, kommer att skapas.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr synonymen format

Solr format stöder motsvarande och explicit synonymen mappningar. Mappningsregler följa öppen källkod synonymen filter specificering av Apache Solr, beskrivs i detta dokument: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Nedan finns en Exempelregel för motsvarande synonymer.
```
USA, United States, United States of America
```

Med regeln ovan, en sökfråga utökas ”USA” till ”USA” eller ”USA” eller ”USA”.

Explicit mappning markeras med en pil ”= >”. Om du angett en term sekvens med en sökfråga som matchar den vänstra sidan av ”= >” ersätts med alternativen på höger sida. Angivna regel nedan sökfrågor ”Washington”, ”Wash.” eller ”WA” kommer alla skrivas till ”WA”. Explicit mappning endast gäller i riktningen som anges och skriv om inte frågan ”WA” till ”Washington” i det här fallet.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista synonymen mappar under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Få en synonym karta under din tjänst.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Ta bort en synonymer karta under din tjänst.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Konfigurera ett sökbara fält om du vill använda synonymen kartan i indexdefinitionen.

En ny fältegenskap **synonymMaps** kan användas för att ange en synonym karta ska användas i en sökbara fält. Synonymen maps är tjänsten Utjämna resurser och kan refereras till av varje fält i ett index under tjänsten.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
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
> Du kan bara ha en synonym mappa per fält i den här förhandsgranskningen. Om du vill använda flera synonymen maps berätta på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Effekten av synonymer på andra sökfunktioner

Funktionen synonymer skrivs den ursprungliga frågan med synonymer med OR-operator. Därför hanterar träffar syntaxmarkering och bedömningen profiler du de ursprungliga termen och synonymer som likvärdiga.

Synonymen funktionen gäller för att söka efter frågor och gäller inte för filter eller facets. På liknande sätt baseras förslag endast på den ursprungliga sikt. synonymen matchar visas inte i svaret.

Synonymen expansion gäller inte för jokertecken söktermer; prefix, fuzzy, och villkor för regex är inte expanderas.

## <a name="tips-for-building-a-synonym-map"></a>Tips för att skapa en synonym karta

- En kortfattad, väl utformad synonymen karta är effektivare än en komplett lista över möjliga matchningar. Alltför stora eller komplexa ordlistor ta längre tid att tolka och påverkar svarstiden fråga om frågan utökas till många synonymer. I stället för att gissa då villkoren kan användas, kan du faktiskt villkoren via en [söka trafik analysrapporten](search-traffic-analytics.md).

- Som både en preliminär och validering utöva aktivera och sedan använda rapporten för att exakt avgöra vilka villkor ska dra nytta av en synonym matchning och sedan fortsätta att använda den som verifiering synonymen kartan ger ett bättre resultat. I rapporten fördefinierade ger paneler ”vanligaste sökfrågor” och ”noll resultatet sökfrågor” dig informationen som behövs.

- Du kan skapa flera synonymen mappningar för search-program (till exempel efter språk om programmet stöder en flerspråkig kundbas). Ett fält kan för närvarande kan bara använda en av dem. Du kan uppdatera synonymMaps-egenskapen för ett fält när som helst.

## <a name="next-steps"></a>Nästa steg

- Om du har ett befintligt index i en utvecklingsmiljö (icke-produktion) kan du experimentera med en liten ordlista nådde Markera om du vill se hur för att lägga till synonymer ändras sökinställningar, inklusive påverkan på bedömningen profiler och förslag.

- [Aktivera sökning trafik analytics](search-traffic-analytics.md) och använda fördefinierade Power BI-rapport för att veta vilka villkor som används mest och vilka som returnerar noll dokument. Tillsammans med dessa insikter, revidera ordlistan om du vill inkludera synonymer för improduktiva frågor som ska matchas till dokument i ditt index.
