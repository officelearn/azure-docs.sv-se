---
title: Azure Cosmos DB indexering principer | Microsoft Docs
description: Förstå hur indexering fungerar i Azure Cosmos-databasen. Lär dig hur du konfigurerar och ändra indexprincip för automatisk indexering och bättre prestanda.
keywords: hur indexering fungerar automatisk indexering, indexering databas
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: d867079b9a5546dc9555697a9066472e4e470977
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298305"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hur fungerar Azure Cosmos DB indexinformationen?

Alla Azure Cosmos DB data indexeras som standard. Även om många kunder är glada över att låta Azure Cosmos DB automatiskt hantera alla aspekter av indexering, kan du ange en anpassad *indexering princip* för samlingar i Azure Cosmos DB när den skapas. Indexering principer i Azure Cosmos DB är mer flexibel och kraftfull än sekundärindex som erbjuds i andra databasplattformar. Du kan utforma och anpassa formen av indexet utan att kompromissa flexibelt schema i Azure Cosmos-databasen. 

Om du vill veta hur indexering fungerar i Azure Cosmos DB, är det viktigt att förstå att du kan göra detaljerade avvägningarna mellan Omkostnad för indexlagring, Skriv- och fråga genomflöde och fråga konsekvenskontroll när du hanterar indexprincip.  

I följande videoklipp visar Azure Cosmos DB Programhanteraren Andrew Liu Azure Cosmos DB automatisk indexering funktioner och hur du ställer in och konfigurera indexprincip på Azure DB som Cosmos-behållare. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

I den här artikeln titta vi Stäng på Azure Cosmos DB indexering principer på hur du anpassar indexprincip och associerade avvägningarna. 

När du har läst den här artikeln kommer du att kunna svara på följande frågor:

* Hur åsidosätta egenskaper för att inkludera eller exkludera från indexering?
* Hur konfigurerar indexet för eventuell uppdateringar?
* Hur kan jag konfigurera indexering för att utföra ORDER BY- eller intervallet frågor?
* Hur gör ändringar till en samling indexprincip?
* Hur jag jämföra lagrings- och prestandakrav för olika indexering principer?

## Anpassa indexprincip i en samling <a id="CustomizingIndexingPolicy"></a>  
Du kan anpassa avvägningarna mellan lagring, Skriv- och prestanda för frågor och fråga konsekvens genom att åsidosätta standardvärdet indexering princip på en Azure DB som Cosmos-samling. Du kan konfigurera följande aspekter:

* **Inkludera eller exkludera dokument och sökvägar till och från indexet**. Du kan exkludera eller inkludera specifika dokument i index när du infoga eller ersätta dokumenten i samlingen. Du kan även inkludera eller exkludera specifika JSON-egenskaper, kallas även *sökvägar*, indexeras i dokument som ingår i ett index. Sökvägar inkluderar mönster med jokertecken.
* **Konfigurera olika typer av indexet**. Du kan ange vilken typ av index sökvägen som krävs för en samling för varje inkluderade sökväg. Du kan ange vilken typ av index baserat på den sökvägen data, förväntade frågan arbetsbelastning och numeriska/strängen ”precision”.
* **Konfigurera index update lägen**. Azure Cosmos-DB stöder tre indexering lägen: konsekvent, Lazy, och inget. Du kan konfigurera indexering lägen via indexprincip på en Azure DB som Cosmos-samling. 

Följande kodavsnitt för Microsoft .NET visar hur du ställer in en anpassad indexprincip när du skapar en samling. I det här exemplet anger vi principen med ett intervall index för strängar och tal på högsta precision. Du kan använda den här principen för att köra ORDER BY-frågor mot strängar.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> JSON-schema för indexprincip ändras med lanseringen av REST API-version 2015-06-03. Med den versionen stöder JSON-schema för indexering princip intervallet index mot strängar. .NET SDK 1.2.0 och Java, Python och Node.js SDK 1.1.0 stöd för det nya schemat för principen. Tidigare versioner av SDK använda REST API för version 2015-04-08. De stöder tidigare schemat för indexering princip.
> 
> Standard indexerar Azure Cosmos DB alla egenskaper för anslutningssträngen i dokument konsekvent med ett Hash-index. Indexeras alla numeriska egenskaper i dokument konsekvent med ett index för intervallet.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Anpassa indexprincip i portalen

Du kan ändra indexprincip i en samling i Azure-portalen: 

1. Gå till Azure DB som Cosmos-konto i portalen och välj sedan din samling. 
2. Välj i den vänstra navigeringsmenyn **inställningar**, och välj sedan **indexering princip**. 
3. Under **indexering princip**, ändra indexprincip och väljer sedan **OK**. 

### Databasen indexering lägen <a id="indexing-modes"></a>  
Azure Cosmos-DB stöder tre indexering lägen som du kan konfigurera via indexprincip på en samling Azure Cosmos DB: konsekvent, Lazy, och inget.

**Konsekvent**: om en samling Azure Cosmos DB principen är konsekvent, samma konsekvensnivå som angetts för plats-läsningar följer du frågor för en specifik Azure DB som Cosmos-samling (stark, begränsat föråldrad, session eller senare). Indexet uppdateras synkront som en del av uppdateringen dokumentet (Infoga, Ersätt, uppdatera och ta bort ett dokument i en samling Azure Cosmos DB).

Konsekvent indexering stöder konsekvent frågor på bekostnad av en eventuell minskning i genomströmning för skrivning. Denna minskning är en funktion av de unika sökvägar som behöver indexeras och ”konsekvensnivå”. Konsekvent indexering läge är utformad för ”skriva snabbt fråga omedelbart” arbetsbelastningar.

**Lazy**: indexet uppdateras asynkront när en samling Azure Cosmos DB overksamt, det vill säga när mängdens genomflödeskapaciteten inte fullt ut för att betjäna användarförfrågningar som.  Observera att du kan få inkonsekventa resultat eftersom data är inhämtas och indexerade långsamt. Detta innebär att din antal frågor eller specifika frågeresultat inte kanske är konsekvent eller repeterbara på den angivna tiden. 

Indexet är vanligtvis i catch-up läge med infogade data. Med Lazy indexering, ändrar tiden live (TTL) resultatet i indexet tas bort och återskapas. Detta gör att antalet och fråga resultaten inkonsekvent under en tidsperiod. De flesta Azure Cosmos DB konton bör använda konsekvent indexering läge.

**Ingen**: en samling som har en ingen indexläge har inget index som är kopplade till den. Detta är vanligt om Azure Cosmos DB används som en nyckel / värde-lagring och dokument används endast av deras ID-egenskap. 

> [!NOTE]
> Om du konfigurerar indexprincip med eftersom ingen har att släppa alla befintliga indexet. Använd det här alternativet om ditt åtkomstmönster kräver endast ID eller självlänkar.
> 
> 

I följande tabell visas konsekvens för frågor baserat på indexering läge (konsekvent och Lazy) som konfigurerats för samlingen samt konsekvensnivå som angetts för frågan förfrågan. Detta gäller för frågor genom att använda alla gränssnitt: REST-API, SDK: er, eller inifrån lagrade procedurer och utlösare. 

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|
|---|---|---|
|Stark|Stark|Eventuell|
|Begränsad föråldrad|Begränsad föråldrad|Eventuell|
|Session|Session|Eventuell|
|Eventuell|Eventuell|Eventuell|

Azure Cosmos-DB Returnerar ett fel för frågor som görs på samlingar som inte har något indexering läge. Frågor kan fortfarande köras som genomsökningar via en explicit **x-ms-documentdb-enable-genomsökning** huvudet i REST-API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Vissa frågefunktioner, som ORDER BY stöds inte som sökningar med **EnableScanInQuery**.

I följande tabell visas konsekvens för frågor baserat på indexering läge (konsekvent Lazy och None) när **EnableScanInQuery** har angetts.

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|Indexering läge: ingen|
|---|---|---|---|
|Stark|Stark|Eventuell|Stark|
|Begränsad föråldrad|Begränsad föråldrad|Eventuell|Begränsad föråldrad|
|Session|Session|Eventuell|Session|
|Eventuell|Eventuell|Eventuell|Eventuell|

I följande exempel visar hur skapa en Azure DB som Cosmos-samling med hjälp av .NET SDK med indexering konsekvent på alla dokument infogningar.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index sökvägar
Azure Cosmos-DB modeller JSON-dokument och indexet som träd. Du kan finjustera principer för sökvägar i trädet. Du kan välja sökvägar till inkludera eller exkludera från att indexera dokument. Detta kan erbjuda bättre skrivprestanda och lägre index lagring för scenarier där frågemönster är kända i förväg.

Index sökvägar som börjar med rot (/) och vanligtvis avslutas med det? jokertecken operator. Detta anger att det finns flera möjliga värden för prefix. Till exempel för att hantera SELECT * FROM familjer F var F.familyName = ”Andersen” måste du inkludera ett index sökvägen för /familyName/? i den samling index princip.

Index sökvägar kan också använda den \* jokertecken operatorn för att ange beteendet för sökvägar rekursivt under prefixet. Till exempel nyttolast / * kan användas för att undanta allt under egenskapen nyttolast från indexering.

Här följer vanliga mönster för att ange index sökvägar:

| Sökväg                | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Standardsökvägen för samlingen. Rekursiva och gäller för hela dokumentträdet.                                                                                                                                                                                                                                   |
| / prop /?             | Index sökväg som krävs för att hantera frågor som följande (med Hash- eller typer respektive):<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                       |
| / prop / *             | Index sökvägen för alla sökvägar under den angivna etiketten. Fungerar med följande frågor<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5<br><br>Välj från samlingen-c WHERE c.prop.subprop.nextprop = ”värde”<br><br>Välj samling c ORDER BY c.prop         |
| [] / sammanställer / /?         | Index sökväg krävs för att hantera iteration och delta i frågor mot matriser av skalärer som [”a”, ”b”, ”c”]:<br><br>Välj taggen från tagg i collection.props var taggen = ”värde”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props där tagga > 5                                                                         |
| /Props/ [] /subprop/? | Index sökväg krävs för att hantera iteration och JOIN-frågor mot matriser av objekt som [{subprop: ”a”}, {subprop: ”b”}]:<br><br>Välj taggen från tagg i collection.props var tag.subprop = ”värde”<br><br>Välj taggen från samlingen c JOIN-tagg i c.props var tag.subprop = ”värde”                                  |
| / prop/subprop /?     | Index sökväg som krävs för att hantera frågor (med Hash- eller typer respektive):<br><br>Välj från samlingen-c WHERE c.prop.subprop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> När du ställer in anpassat index sökvägar du krävs för att ange standard indexering regel för trädet hela dokumentet som markerats med särskilda sökvägen ”/ *”. 
> 
> 

I följande exempel konfigureras en specifik sökväg med områdesindex och anpassade Precisionvärdet 20 byte:

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
Du har flera alternativ när du konfigurerar indexprincip för en sökväg. Du kan ange en eller flera indexering definitioner för varje sökväg:

* **Datatypen**: sträng, siffra, punkt, Polygon eller LineString (kan innehålla endast en post per datatyp och sökväg).
* **Index-typ**: Hash (likhetsfrågor), intervall (likhetsfrågor, intervall eller ORDER BY-frågor) eller Spatial (spatial frågor).
* **Precision**: för ett Hash-index detta varierar från 1 till 8 för både strängar och siffror. Standardvärdet är 3. Det här värdet kan vara -1 (maximal precision) för ett intervall index. Det kan variera finns mellan 1 och 100 (maximal precision) för sträng eller numeriska värden.

#### <a name="index-kind"></a>Typ av index
Azure Cosmos-DB stöder Hash-index och intervallet index typer för varje sökväg som kan konfigureras för datatyperna String eller en siffra eller båda.

* **Hash** stöder effektiv likhetsfrågor och JOIN-frågor. För de flesta användningsfall behöver Hash-index en högre precision än standardvärdet 3 byte. Datatypen kan vara en sträng eller en siffra.
* **Intervallet** stöder effektiv likhetsfrågor intervallet frågor (med hjälp av >, <>, =, < =,! =), och ORDER BY-frågor. ORDER By-frågor som standard även kräva maximala index precision (-1). Datatypen kan vara en sträng eller en siffra.

Azure Cosmos-DB stöder också Spatial index-typ för varje sökväg som kan anges för datatyperna punkt, Polygon eller LineString. Värdet på den angivna sökvägen måste vara ett giltigt GeoJSON-fragment som `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** stöder effektiv spatial (inom och avstånd) frågor. Datatypen kan vara punkt, Polygon eller LineString.

> [!NOTE]
> Azure Cosmos-DB har stöd för automatisk indexering av punkt och Polygon LineString datatyper.
> 
> 

Här följer stöds index typer och exempel på frågor som de kan användas för att hantera:

| Typ av index | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash-över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Hash över/sammanställer / [] /? (eller / eller/sammanställer /) kan användas för att effektivt hantera följande frågor:<br><br>Välj taggen från samlingen c JOIN-tagg i c.props var taggen = 5                                                                                                                       |
| Intervall      | Intervallet över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”värde”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Intervallet över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen c<br><br>VAR ST_DISTANCE (c.prop, {”typ”: ”plats”, ”coordinates”: [0.0, 10.0]}) < 40<br><br>Välj från samlingen c där ST_WITHIN(c.prop, {"type": "Polygon",...})--med indexering punkter aktiverad<br><br>Välj från samlingen c där ST_WITHIN({"type": "Point",...}, c.prop)--med indexering på polygoner aktiverad              |

Som standard returneras ett fel för frågor med intervallet operatorer som > = om det finns inga intervall index (för alla precision) att signalera att en genomsökning kan vara nödvändigt att hantera frågan. Intervallet frågor kan utföras utan ett intervall index med hjälp av den **x-ms-documentdb-enable-genomsökning** huvudet i REST-API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Om det finns andra filter i frågan som Azure Cosmos DB kan använda indexet för att filtrera mot, returneras inget fel.

Samma regler gäller för spatial frågor. Som standard returneras ett fel för spatial frågor om det finns inget spatial index och det finns inga filter som kan hanteras från indexet. De kan utföras en genomsökning med hjälp av **x-ms-documentdb-enable-genomsökning** eller **EnableScanInQuery**.

#### <a name="index-precision"></a>Index precision
Du kan använda index precision för att kompromissa mellan index lagringsutrymmet omkostnader och prestanda för frågor. För tal rekommenderar vi använder standardkonfigurationen för precision-1 (max). Eftersom siffror är 8 byte i JSON kan motsvarar detta en konfiguration på 8 byte. Att välja ett lägre värde för precision, till exempel 1 och 7, indexposten innebär att värdena inom vissa intervall som mappas till samma. Därför du minska index lagringsutrymme, men frågan kanske har att bearbeta fler dokument. Därför måste förbrukar den mer genomflöde i frågeenheter.

Index precision konfigurationen har mer praktiska program med strängen intervall. Eftersom strängar kan vara en godtycklig längd, kan valet av indexet precision påverka prestanda för sträng intervallet frågor. Det kan även påverka mängden index lagringsutrymme som krävs. Sträng intervallet index kan konfigureras med 1 till 100 eller -1 (max). Om du vill utföra ORDER BY-frågor mot egenskaperna för anslutningssträngen måste du ange en noggrannhet på 1 för motsvarande sökvägar.

Rumsindex använda alltid standard index precisionen för alla typer (Point, LineString och Polygon). Standard index precisionen för rumsindex kan inte åsidosättas. 

I följande exempel visas hur du ökar precisionen för intervallet index i en samling med hjälp av .NET SDK. 

**Skapa en samling med ett anpassat index precision**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos-DB Returnerar ett fel när en fråga som använder ORDER BY men inte har en områdesindex mot den efterfrågade sökvägen med högsta precision. 
> 
> 

Du kan dessutom helt exkludera sökvägar från indexering. I nästa exempel visas hur du undantar en hela avsnittet dokument (en *underträd*) från att indexera med hjälp av den \* jokertecken operator.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Delta och välja att inaktivera indexering
Du kan välja om du vill att samlingen som automatiskt indexerar alla dokument. Alla dokument indexeras automatiskt som standard, men du kan inaktivera automatisk indexering. När indexering är avstängd dokument kan nås bara via deras självlänkar eller frågor med hjälp av dokumentet-ID.

Du kan fortfarande selektivt lägga till endast vissa dokument med automatisk indexering avstängd, i indexet. Däremot kan du lämna automatisk indexering på och selektivt väljer att undanta vissa dokument. Indexering på/av konfigurationer är användbara när du har bara en del av dokument som ska efterfrågas.

I följande exempel visas hur du lägger till ett dokument uttryckligen med hjälp av den [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) och [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) egenskapen.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Ändra indexprincip i en samling
I Azure Cosmos DB, kan du ändra indexprincip i en samling direkt. En ändring i indexering princip på en samling Azure Cosmos DB kan leda till en ändring i form av indexet. Ändringen påverkar sökvägar som kan indexeras, deras precision och konsekvent modell för indexet sig själv. En ändring i indexering princip effektivt kräver en transformering av gammalt index till ett nytt index. 

**Online index omvandlingar**

![Hur indexering fungerar – Azure Cosmos DB online index omvandlingar](./media/indexing-policies/index-transformations.png)

Index transformationer görs online. Det innebär att de indexerade per gamla principen effektivt omvandlas per den nya principen *utan att påverka tillgängligheten för skrivning eller etablerat dataflöde* i mängden. Konsekvenskontroll av Läs- och skrivåtgärder som gjorts med hjälp av REST-API, SDK: er, eller inifrån lagrade procedurer och utlösare påverkas inte under omvandling av indexet. Det finns ingen prestandaförsämring eller driftstopp för dina appar när du gör en indexprincip ändra.

Under den tid som index omvandling är pågår, är dock frågor överensstämmelse oavsett indexering läge är konfigurerad (konsekvent eller Lazy). Detta gäller även för frågor från alla gränssnitt: REST-API, SDK: er, och inifrån lagrade procedurer och utlösare. Precis som med Lazy indexering, utförs omvandling av indexet asynkront i bakgrunden på replikerna med hjälp av ledig resurser som är tillgängliga för en viss replik. 

Index transformationer görs också på plats. Azure Cosmos-DB upprätthålla inte två kopior av index och Byt ut det gamla indexet med den nya. Detta innebär att inga ytterligare diskutrymme krävs eller förbrukas i samlingar medan index omvandlingar utförs.

När du ändrar indexprincip, tillämpas ändringar för att flytta från det gamla indexet till den nya främst utifrån indexering läge konfigurationer. Indexering läge konfigurationer spela en viktigare roll än andra värden som inkluderad/exkluderad sökvägar, index-typer och Precision-datorerna. 

Om ditt gamla och nya både principer använder konsekvent indexering, utför Azure Cosmos DB en onlineåtgärd omvandling. Du kan inte använda en annan indexering ändring av principer som har konsekvent indexering läge medan omvandlingen pågår. Du kan dock flytta till Lazy eller ingen indexering läge under en transformering pågår: 

* När du flyttar till Lazy är index principändring gälla omedelbart. Azure Cosmos-DB startar återskapa indexet asynkront. 
* När du flyttar till None, släppa indexet omedelbart. Flytta till None är användbart när du vill avbryta en pågående omvandling och börja om från början med en annan indexprincip. 

Följande kodavsnitt visar hur du ändrar en samling indexprincip från konsekvent indexerings-läge till Lazy indexerings-läge. Om du använder .NET SDK kan startar du en indexering principändring med hjälp av den nya **ReplaceDocumentCollectionAsync** metod.

**Ändra indexprincip från konsekvent till Lazy**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Spåra förloppet för omvandling av index**

Du kan spåra procentandel förloppet för omvandling av index till ett konsekvent index med hjälp av den **IndexTransformationProgress** svar egenskap från en **ReadDocumentCollectionAsync** anropa. Andra SDK: er och REST-API stöd för motsvarande egenskaper och metoder för att göra ändringar av indexerings-principer. Du kan kontrollera förloppet för en omvandling av index till ett konsekvent index genom att anropa **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * Den **IndexTransformationProgress** egenskapen gäller bara när du omvandlar en konsekvent index. Använd den **ResourceResponse.LazyIndexingProgress** egenskapen för att spåra transformationer till ett Lazy-index.
> * Den **IndexTransformationProgress** och **LazyIndexingProgress** egenskaper fylls endast för en samling som partitionerade som är en samling som har skapats utan någon partitionsnyckel.
>

Du kan släppa indexet för en samling genom att flytta till ingen indexering läge. Det kan vara användbart operativa om du vill avbryta en pågående omvandling och starta en ny direkt.

**DROP index för en samling**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

När blir du indexering principändringar till Azure DB som Cosmos-samlingar Följande är de vanligaste användningsområden:

* Hantera enhetliga resultat under normal drift, men återgår till att Lazy indexering läge under bulk data import.
* Börja använda nya funktioner för fulltextindexering på din aktuella Azure DB som Cosmos-samlingar. Du kan exempelvis använda geospatiala frågar, vilket kräver Spatial index-typ eller ORDER BY- / sträng intervallet frågor, vilket kräver att strängen intervallet index typ.
* Hand – Välj egenskaper som indexeras och ändra dem över tid.
* Finjustera indexering precision att förbättra frågeprestanda eller minska lagringsutrymme som förbrukas.

> [!NOTE]
> Att ändra indexprincip genom att använda **ReplaceDocumentCollectionAsync**, måste du använda version 1.3.0 eller en senare version av .NET SDK.
> 
> Se till att det finns tillräckligt med ledigt lagringsutrymme på samlingen för omvandling av indexet ska kunna slutföras. Om samlingen når sin lagringskvot, har index-transformation pausats. Index omvandling återupptas automatiskt när lagringsutrymme som är tillgängliga, till exempel om du tar bort vissa dokument.
> 
> 

## <a name="performance-tuning"></a>Prestandajustering
SQL-API: er innehåller information om prestandavärden, till exempel index lagringsutrymme som används och genomströmning kostnaden (frågeenheter) för varje åtgärd. Du kan använda den här informationen för att jämföra olika principer för indexering, och för prestandajustering.

Kontrollera lagringskvoten och användning av en samling genom att köra en **HEAD** eller **hämta** begäran mot samlingen resursen. Granska sedan de **x-ms-begäran-quota** och **x-ms-begäran-användning** huvuden. I .NET-SDK på [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) och [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) egenskaper i [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) innehåller dessa motsvarande värden.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Att mäta arbetet med att indexera i varje skrivåtgärd (skapa, uppdatera eller ta bort) granska de **x-ms-begäran-kostnad** huvud (eller motsvarande [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) egenskap i [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) i .NET SDK) att mäta antalet frågeenheter som förbrukas av dessa åtgärder.

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
En ändring i schemat för indexering princip introducerades 7 juli 2015 med REST API-version 2015-06-03. Motsvarande klasser i SDK-versioner har nya implementeringar så att den matchar schemat. 

Följande ändringar har införts i JSON-specifikationen:

* Indexering princip stöder intervallet index för strängar.
* Varje sökväg kan ha flera definitioner av indexet. Det kan ha en för varje datatyp.
* Indexering precision stöder 1 till 8 för tal, 1 till 100 för strängar och -1 (maximal precision).
* Sökvägssegment behöver inte ha ett citattecken för att undvika varje sökväg. Du kan till exempel lägga till en sökväg för   **/rubrik /?** i stället för **/ ”title” /?**.
* Rotsökvägen som representerar ”alla sökvägar” kan representeras som **/ \*** (förutom **/**).

Om du har kod som tillhandahåller samlingar med en anpassad indexprincip som skrivits med .NET SDK-version 1.1.0 eller en tidigare version om du vill flytta till SDK version 1.2.0, måste du ändra din programkod för att hantera de här ändringarna. Om du inte har koden som konfigurerar indexprincip eller om du vill fortsätta med en tidigare version av SDK några ändringar krävs.

Här är ett exempel på en anpassad indexprincip som skrivits med hjälp av REST API-version 2015-06-03, följt av samma indexprincip som skrivits med hjälp av det tidigare REST-API-version 2015-04-08 en praktiska jämförelse.

**Aktuella indexering princip-JSON (REST API version 2015-06-03)**

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


**Tidigare indexering princip-JSON (REST API version 2015-04-08)**

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


## <a name="next-steps"></a>Nästa steg
För index princip för hantering av prover och för att lära dig mer om Azure DB som Cosmos-frågespråket finns i följande länkar:

* [SQL API .NET index management-kodexempel](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Åtgärder för insamling av SQL API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Fråga med SQL](sql-api-sql-query.md)

