---
title: Azure Cosmos DB indexering principer | Microsoft Docs
description: "Förstå hur indexering fungerar i Azure Cosmos-databasen. Lär dig hur du konfigurerar och ändra indexprincip för automatisk indexering och bättre prestanda."
keywords: hur indexering fungerar automatisk indexering, indexering databas
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: 30a21645831f0cfcb3b52c797dbddfa6b5283960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hur fungerar Azure Cosmos DB indexinformationen?

Alla Azure Cosmos DB data indexeras som standard. Och många kunder är glada över att låta Azure Cosmos DB automatiskt hantera alla aspekter av indexering, Azure Cosmos DB också stöd för att ange en anpassad **indexering princip** för samlingar när du skapar. Indexering principer i Azure Cosmos DB är mer flexibel och kraftfull än sekundärindex som erbjuds i andra databasplattformar,, eftersom de låter dig utforma och anpassa formen av indexet utan att kompromissa flexibelt schema. Om du vill veta hur indexering fungerar i Azure Cosmos DB, måste du förstå som du kan göra detaljerade kompromisser mellan Omkostnad för indexlagring, Skriv- och fråga genomflöde och fråga konsekvens genom att hantera indexprincip.  

I den här artikeln vi titta Stäng på Azure Cosmos DB indexering principer, hur du kan anpassa indexprincip och associerade avvägningarna. 

När du har läst den här artikeln kommer du att kunna svara på följande frågor:

* Hur åsidosätta egenskaper för att inkludera eller exkludera från indexering?
* Hur konfigurerar indexet för eventuell uppdateringar?
* Hur kan jag konfigurera indexering för att utföra Order By- eller intervallet frågor?
* Hur gör ändringar till en samling indexprincip?
* Hur jag jämföra lagrings- och prestandakrav för olika indexering principer?

## <a id="CustomizingIndexingPolicy"></a>Anpassa indexprincip i en samling
Utvecklare kan anpassa avvägningarna mellan lagring, Skriv-/ frågeprestanda och fråga konsekvens genom att åsidosätta standardprincipen för fulltextindexering på en samling Azure Cosmos DB och konfigurera följande aspekter.

* **Inklusive/exklusive dokument och sökvägar till/från index**. Utvecklare kan välja vissa dokument som ska uteslutas eller ingår i indexet vid tidpunkten för att infoga eller ersätta dem till samlingen. Utvecklare kan också välja att inkludera eller exkludera kallas även vissa JSON-egenskaper sökvägar (inklusive mönster med jokertecken) med att indexera mellan dokument som ingår i ett index.
* **Konfigurera olika indexera typer**. För alla sökvägar som tas kan utvecklare även ange vilken typ av index som de kräver över en samling baserat på deras data och förväntas arbetsbelastningen för frågan och numeriska/strängen ”precision” för varje sökväg.
* **Konfigurera Index Update lägen**. Azure Cosmos-DB stöder tre indexering lägen som kan konfigureras via indexprincip på en samling Azure Cosmos DB: konsekvent, Lazy och None. 

Följande kodavsnitt i .NET visar hur du ställer in en anpassad indexprincip under genereringen av en samling. Här ange vi principen med områdesindex för strängar och tal i den maximala precisionen. Den här principen kan vi köra Order By-frågor mot strängar.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> JSON-schema för indexprincip har ändrats med lanseringen av REST API version 2015-06-03 att stödja intervallet index mot strängar. .NET SDK 1.2.0 och Java, Python och Node.js SDK 1.1.0 stöd för det nya schemat för principen. Äldre SDK REST-API: n version 2015-04-08 och stöd för äldre schemat för indexering av principen.
> 
> Som standard indexerar Azure Cosmos DB alla egenskaper för anslutningssträngen i dokument konsekvent med ett Hash-index och numeriska egenskaper med ett index för intervallet.  
> 
> 

### <a name="customizing-the-indexing-policy-using-the-portal"></a>Anpassa den indexprincip med hjälp av portalen

Du kan ändra indexprincip i en samling med Azure-portalen. Öppna Azure DB som Cosmos-konto i Azure portal, Välj din samling i det vänstra navigeringsfönstret och klicka **inställningar**, och klicka sedan på **indexering princip**. I den **indexering princip** bladet ändra indexprincip och klicka sedan på **OK** att spara ändringarna. 

### <a id="indexing-modes"></a>Databasen indexering lägen
Azure Cosmos-DB stöder tre indexering lägen som kan konfigureras via indexprincip på en samling Azure Cosmos DB – konsekvent, Lazy och None.

**Konsekvent**: om en samling Azure Cosmos DB princip utses ”konsekvent”, frågor om Azure Cosmos DB samling följer konsekvenskontroll samma nivå som angetts för plats-läsningar (d.v.s. stark, begränsat föråldrad, session eller eventuell). Indexet uppdateras synkront som en del av dokument-uppdateringen (d.v.s. insert-, Ersätt, uppdatering och borttagning av ett dokument i en samling Azure Cosmos DB).  Konsekvent indexering stöder konsekvent frågor på bekostnad av eventuell minskning i genomströmning för skrivning. Denna minskning är en funktion av de unika sökvägar som behöver indexeras och ”konsekvensnivå”. Konsekvent indexering läge är utformad för ”skriva snabbt fråga omedelbart” arbetsbelastningar.

**Lazy**: Om du vill tillåta högsta dokumentet införandet genomströmning en samling Azure Cosmos DB kan konfigureras med lazy konsekvenskontroll; betydelse frågorna är överensstämmelse. Indexet uppdateras asynkront när en samling Azure Cosmos DB overksamt d.v.s. när mängdens genomflödeskapaciteten inte fullt ut för att betjäna användarförfrågningar som. För ”mata in nu fråga senare” arbetsbelastningar som kräver röra sig obehindrat dokumentet införandet är ”lazy” indexering läge lämpligt.

**Ingen**: en samling som markerats med index ”None” har inget index som är kopplade till den. Detta är vanligt om Azure Cosmos DB används som en nyckel / värde-lagring och dokument används endast av deras ID-egenskap. 

> [!NOTE]
> Om du konfigurerar indexprincip med ”None” har att släppa alla befintliga indexet. Använd det här alternativet om ditt åtkomstmönster bara behöver ”id” och/eller ”automatisk länka”.
> 
> 

I följande tabell visas konsekvens för frågor baserat på indexering läge (konsekvent och Lazy) som konfigurerats för samlingen samt konsekvensnivå som angetts för frågan förfrågan. Detta gäller för frågor som gjorts med alla gränssnitt - REST API-SDK: er eller inifrån lagrade procedurer och utlösare. 

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|
|---|---|---|
|Stark|Stark|Eventuell|
|Begränsad föråldring|Begränsad föråldring|Eventuell|
|Session|Session|Eventuell|
|Eventuell|Eventuell|Eventuell|

Azure Cosmos-DB Returnerar ett fel för frågor som gjorts på samlingar med ingen indexering läge. Frågor kan fortfarande köras som genomsökningar via en explicit `x-ms-documentdb-enable-scan` huvudet i REST-API eller `EnableScanInQuery` begära alternativet med .NET SDK. Vissa frågefunktioner som ORDER BY stöds inte som sökningar med `EnableScanInQuery`.

I följande tabell visas konsekvens för frågor baserat på indexering läge (konsekvent Lazy och None) när EnableScanInQuery har angetts.

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|Indexering läge: ingen|
|---|---|---|---|
|Stark|Stark|Eventuell|Stark|
|Begränsad föråldring|Begränsad föråldring|Eventuell|Begränsad föråldring|
|Session|Session|Eventuell|Session|
|Eventuell|Eventuell|Eventuell|Eventuell|

I följande exempel visar hur skapa en Azure DB som Cosmos-samling med .NET SDK med indexering konsekvent på alla dokument infogningar.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index sökvägar
Azure Cosmos-DB modeller JSON-dokument och indexet som träd och gör att du kan finjustera principer för sökvägar i trädet. Dokument, kan du välja vilka sökvägar måste inkluderas eller uteslutas från indexering. Detta kan erbjuda bättre skrivprestanda och lägre index lagring för scenarier när frågemönster är kända i förväg.

Index sökvägar som börjar med rot (/) och vanligtvis avslutas med det? jokertecken operator, som anger att det finns flera möjliga värden för prefix. Till exempel för att hantera SELECT * FROM familjer F var F.familyName = ”Andersen” måste du inkludera ett index sökvägen för /familyName/? i den samling index princip.

Index sökvägar kan också använda den * jokertecken operatorn för att ange beteendet för sökvägar rekursivt under prefixet. Till exempel nyttolast / * kan användas för att undanta allt under egenskapen nyttolast från indexering.

Här följer vanliga mönster för att ange index sökvägar:

| Sökväg                | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Standardsökvägen för samlingen. Rekursiva och gäller för hela dokumentträdet.                                                                                                                                                                                                                                   |
| / prop /?             | Index sökväg som krävs för att hantera frågor som följande (typer respektive med hash- eller intervall):<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                       |
| / prop / *             | Index sökvägen för alla sökvägar under den angivna etiketten. Fungerar med följande frågor<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5<br><br>Välj från samlingen-c WHERE c.prop.subprop.nextprop = ”värde”<br><br>Välj samling c ORDER BY c.prop         |
| [] / sammanställer / /?         | Index sökväg krävs för att hantera iteration och delta i frågor mot matriser av skalärer som [”a”, ”b”, ”c”]:<br><br>Välj taggen från tagg i collection.props var taggen = ”värde”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props där tagga > 5                                                                         |
| /Props/ [] /subprop/? | Index sökväg krävs för att hantera iteration och JOIN-frågor mot matriser av objekt som [{subprop: ”a”}, {subprop: ”b”}]:<br><br>Välj taggen från tagg i collection.props var tag.subprop = ”värde”<br><br>Välj taggen från samlingen c JOIN-tagg i c.props var tag.subprop = ”värde”                                  |
| / prop/subprop /?     | Index sökväg som krävs för att hantera frågor (typer respektive med hash- eller intervall):<br><br>Välj från samlingen-c WHERE c.prop.subprop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> När anpassat index sökvägar, du måste ange standard indexering regel för trädet hela dokumentet med särskilda sökvägen ”/ *”. 
> 
> 

I följande exempel konfigureras en specifik sökväg med intervallet indexering och anpassade Precisionvärdet 20 byte:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Index-datatyper, typer och Precision-datorerna
Nu när vi har valt en titt på hur du anger sökvägar, ska vi titta på de alternativ som vi kan använda för att konfigurera indexprincip för en sökväg. Du kan ange en eller flera indexering definitioner för varje sökväg:

* Datatyp: **sträng**, **nummer**, **punkt**, **Polygon**, eller **LineString** (kan endast innehålla en post per datatyp per sökväg)
* Index-typ: **Hash** (likhetsfrågor) **intervallet** (likhetsfrågor, intervall eller Order By-frågor) eller **Spatial** (spatial frågor) 
* Precision: För hash-index detta varierar från 1 till 8 för både strängar och tal med en standard som 3. För områdesindex det här värdet vara 1 (maximal precision) och variera mellan 1-100 (maximal precision) för sträng eller numeriska värden.

#### <a name="index-kind"></a>Typ av index
Azure Cosmos-DB stöder Hash och intervallet index typer för varje sökväg (som kan konfigureras för strängar, siffror eller båda).

* **Hash** stöder effektiv likhetsfrågor och JOIN-frågor. Hash-index behöver inte en högre precision än standardvärdet 3 byte för de flesta användningsfall. Datatyp kan vara en sträng eller en siffra.
* **Intervallet** stöder effektiv likhetsfrågor intervallet frågor (med hjälp av >, <>, =, < =,! =), och Order By-frågor. Order By-frågor som standard även kräva maximala index precision (-1). Datatyp kan vara en sträng eller en siffra.

Azure Cosmos-DB stöder också Spatial index-typ för varje sökväg som kan anges för datatyperna punkt, Polygon eller LineString. Värdet på den angivna sökvägen måste vara ett giltigt GeoJSON-fragment som `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** stöder effektiv spatial (inom och avstånd) frågor. Datatyp kan vara punkt, Polygon eller LineString.

> [!NOTE]
> Azure Cosmos-DB har stöd för automatisk indexering av punkter och polygoner LineStrings.
> 
> 

Här följer stöds index typer och exempel på frågor som de kan användas för att hantera:

| Typ av index | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash-över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Hash över/sammanställer / [] /? (eller / eller/sammanställer /) kan användas för att effektivt hantera följande frågor:<br><br>Välj taggen från samlingen c JOIN-tagg i c.props var taggen = 5                                                                                                                       |
| intervallet      | Intervallet över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Intervallet över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen c<br><br>VAR ST_DISTANCE (c.prop, {”typ”: ”plats”, ”coordinates”: [0.0, 10.0]}) < 40<br><br>Välj från samlingen c där ST_WITHIN(c.prop, {"type": "Polygon",...})--med indexering punkter aktiverad<br><br>Välj från samlingen c där ST_WITHIN({"type": "Point",...}, c.prop)--med indexering på polygoner aktiverad              |

Som standard returneras ett fel för frågor med intervallet operatorer som > = om det finns inga intervall index (för alla precision) för att signalera att en genomsökning kan vara nödvändigt att hantera frågan. Intervallet frågor kan utföras utan ett intervall index med x-ms-documentdb-enable-genomsökning huvudet i REST-API eller alternativet EnableScanInQuery som med .NET SDK. Om det finns andra filter i frågan att Azure Cosmos DB kan använda indexet för att filtrera mot sedan inga fel returneras.

Samma regler gäller för spatial frågor. Som standard returneras ett fel för spatial frågor om det finns inget spatial index och det finns inga filter som kan hanteras från indexet. De kan utföras som en genomsökning med x-ms-documentdb-enable-genomsökning/EnableScanInQuery.

#### <a name="index-precision"></a>Index precision
Index precision kan du väger index lagring omkostnader och prestanda för frågor. För tal rekommenderar vi använder standardkonfigurationen för precision-1 (”högsta”). Eftersom siffror är 8 byte i JSON är motsvarar detta en konfiguration på 8 byte. Du väljer ett lägre värde för precision, till exempel 1-7, innebär att värdena inom vissa intervall som mappas till samma indexposten. Därför minskar du index lagringsutrymme, men Frågekörningen kanske har att bearbeta flera dokument och därför använda mer genomströmning d.v.s. enheter för programbegäran.

Index precision konfigurationen har mer praktiska program med strängen intervall. Eftersom strängar kan vara en godtycklig längd, kan valet av indexet precision påverka prestanda för sträng intervallet frågor och påverka mängden index lagringsutrymme som krävs. Strängen intervallet index kan konfigureras med 1-100 eller -1 (”högsta”). Om du vill utföra Order By-frågor mot egenskaperna för anslutningssträngen måste du ange en noggrannhet på 1 för motsvarande sökvägar.

Rumsindex alltid använda standard index för alla typer (poäng, LineStrings och polygoner) och kan inte ändras. 

I följande exempel visas hur du ökar precisionen för intervallet index i en samling med .NET SDK. 

**Skapa en samling med ett anpassat index precision**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos-DB Returnerar ett fel när en fråga som använder Order By men har inte en områdesindex mot den efterfrågade sökvägen med högsta precision. 
> 
> 

På liknande sätt kan sökvägar inte helt uteslutas från indexering. I nästa exempel visas hur du undantar en hela avsnittet dokument (kallas även ett underträd) från att använda indexering av ”*” jokertecken.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Börjar och väljer bort indexering
Du kan välja om du vill att samlingen som automatiskt indexerar alla dokument. Alla dokument indexeras automatiskt som standard, men du kan välja att inaktivera den. När indexering är avstängd dokument kan nås bara via deras självlänkar eller av frågor med ID: t.

Du kan fortfarande selektivt lägga till endast vissa dokument med automatisk indexering avstängd, i indexet. Däremot kan du lämna automatisk indexering på och selektivt välja att utesluta endast vissa dokument. Indexering på/av konfigurationer är användbara när du har bara en del av dokument som behöver efterfrågas.

Till exempel i följande exempel visas hur du lägger till ett dokument som uttryckligen med hjälp av den [DocumentDB API .NET SDK](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-sdk-dotnet) och [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) egenskapen.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Ändra indexprincip i en samling
Azure Cosmos-DB kan du göra ändringar i indexprincip i en samling direkt. En ändring i indexering princip på en samling Azure Cosmos DB kan leda till en ändring i form av indexet inklusive sökvägar kan indexeras, deras precision samt indexet själva konsekvent modell. Därmed kan en ändring i indexprincip, effektivt kräver en transformering av gammalt index till en ny. 

**Online Index omvandlingar**

![Hur indexering fungerar – Azure Cosmos DB online index omvandlingar](./media/indexing-policies/index-transformations.png)

Index transformationer görs online, vilket innebär att de indexerade per gamla principen effektivt omvandlas per den nya principen **utan att påverka tillgängligheten för skrivning eller etablerat dataflöde** i mängden. Konsekvenskontroll av Läs- och skrivåtgärder som skapats med hjälp av REST-API, SDK: er eller inifrån lagrade procedurer och utlösare påverkas inte under omvandling av indexet. Det innebär att det finns ingen prestandaförsämring eller driftstopp för dina appar när du gör en indexprincip ändra.

Under den tid som index omvandling är pågår, är dock frågor överensstämmelse oavsett indexering läge är konfigurerad (konsekvent eller Lazy). Detta gäller för frågor från alla gränssnitt – REST API SDK: er, och inifrån lagrade procedurer och utlösare. Precis som med Lazy indexering, utförs omvandling av indexet asynkront i bakgrunden på replikerna med hjälp av ledig tillgängliga resurser för en viss replik. 

Index transformationer görs också **i situ** (på plats), d.v.s. Azure Cosmos-DB inte upprätthålla två kopior av index och byta ut gammal indexet med den nya. Detta innebär att inga ytterligare diskutrymme krävs eller förbrukas i samlingar när du utför index transformationer.

När du ändrar indexprincip exkluderad hur ändringarna tillämpas för att flytta från det gamla indexet till den nya en beror främst på indexering läge konfigurationer viktigare än andra värden som inkluderad/sökvägar, index-typer och Precision-datorerna. Om både de gamla och nya principerna använder konsekvent indexering, utför Azure Cosmos DB en onlineåtgärd transformation. Du kan inte använda en annan indexering principändring konsekvent indexering läge medan omvandlingen pågår.

Du kan dock flytta Lazy eller ingen indexering läge under en transformering pågår. 

* När du flyttar till Lazy principändring index görs gällande omedelbart och Azure Cosmos DB börjar återskapa indexet asynkront. 
* När du flyttar till None, har sedan indexet släppts gälla omedelbart. Flytta till None är användbart när du vill avbryta en pågående omvandling och starta ny med en annan indexprincip. 

Om du använder .NET SDK kan startar du en indexering principändring med hjälp av den nya **ReplaceDocumentCollectionAsync** metod och spåra förloppet procentandel av omvandling index med det  **IndexTransformationProgress** svar egenskap från en **ReadDocumentCollectionAsync** anropa. Andra SDK: er och REST-API: stöd för motsvarande egenskaper och metoder för att göra ändringar av indexerings-principer.

Här är ett kodfragment som visar hur du ändrar en samling indexprincip från konsekvent indexerings-läge till Lazy.

**Ändra Indexprincip från konsekvent till Lazy**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Du kan kontrollera förloppet för en index-transformation genom att anropa ReadDocumentCollectionAsync, till exempel som visas nedan.

**Spåra förloppet för omvandling av Index**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Du kan släppa indexet för en samling genom att flytta till ingen indexering läge. Det kan vara användbart operativa om du vill avbryta en pågående omvandling och starta en ny direkt.

**Släppa indexet för en samling**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

När blir du indexering principändringar till Azure DB som Cosmos-samlingar Följande är de vanligaste användningsområden:

* Hantera enhetliga resultat under normal drift, men faller tillbaka till lazy indexering under bulk data import
* Börjar använda nya indexering funktioner på din aktuella Azure DB som Cosmos-samlingar, t.ex. som geospatiala fråga som kräver Spatial index-typ eller Order By / sträng intervallet frågor som kräver sträng intervallet index typ
* Sidan Välj egenskaperna som indexeras och ändra dem över tid
* Finjustera indexering precision om du vill förbättra frågeprestanda eller minska lagringsutrymme som förbrukas

> [!NOTE]
> Om du vill ändra indexprincip med ReplaceDocumentCollectionAsync du behöver version > = 1.3.0 av .NET SDK
> 
> För index omvandling ska slutföras, måste du se till att det finns tillräckligt med ledigt lagringsutrymme på samlingen. Om samlingen når sin lagringskvot, pausas index-transformation. Index omvandling återupptas automatiskt när lagringsutrymmet är tillgängligt, t.ex. Om du tar bort vissa dokument.
> 
> 

## <a name="performance-tuning"></a>Prestandajustering
DocumentDB APIs ange information om prestandamått, till exempel index lagringsutrymme som används och genomströmning kostnaden (frågeenheter) för varje åtgärd. Den här informationen kan användas för att jämföra olika principer för indexering och för prestandajustering.

Kör en HEAD eller GET-begäran mot samlingen resurs för att kontrollera lagringskvoten och användning av en samling och inspektera x-ms-begäran-quota och x-ms-begäran-användning-rubriker. I .NET-SDK på [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) och [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) egenskaper i [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) innehåller dessa motsvarande värden.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Att mäta arbetet med att indexera i varje skrivåtgärd (skapa, uppdatera eller ta bort) inspektera huvudet x-ms-begäran-tillägget (eller motsvarande [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) egenskap i [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) i .NET SDK) att mäta antalet begäran enheter som används av dessa åtgärder.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Ändringar av indexering principspecifikationen
En ändring i schemat för indexprincip introducerades 7 juli 2015 med REST API-version 2015-06-03. Motsvarande klasser i SDK-versioner har nya implementeringar så att den matchar schemat. 

Följande ändringar har införts i JSON-specifikationen:

* Indexering princip stöder intervallet index för strängar
* Varje sökväg kan ha flera index definitioner, ett för varje datatyp
* Indexering precision har stöd för 1 – 8 för tal 1-100 för strängar och -1 (maximal precision)
* Sökvägar segment kräver inte ett citattecken för att undvika varje sökväg. Exempelvis kan du lägga till en sökväg för/rubrik /? i stället för / ”title” /?
* Rotsökvägen som representerar ”alla sökvägar” kan representeras som / * (förutom /)

Om du har kod som tillhandahåller samlingar med en anpassad indexprincip som skrivits med version 1.1.0 av .NET SDK eller äldre behöver du ändra din programkod för att hantera dessa ändringar för att flytta till SDK version 1.2.0. Om du inte har kod som konfigurerar indexprincip eller planerar att använda en äldre version av SDK, krävs några ändringar.

En praktisk jämförelse är här ett exempel anpassad indexprincip skrivs med hjälp av REST API-version 2015-06-03 samt föregående version 2015-04-08.

**Tidigare Indexprincip JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }

**Aktuella Indexprincip JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## <a name="next-steps"></a>Nästa steg
Följ länkarna nedan för index princip för hantering av prover och vill veta mer om Azure Cosmos DB frågespråk.

1. [DocumentDB API .NET indexhantering kodexempel](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Åtgärder för insamling av DocumentDB API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Fråga med SQL](documentdb-sql-query.md)

