---
title: Azure Cosmos DB indexeringsprinciper | Microsoft Docs
description: Förstå hur indexering fungerar i Azure Cosmos DB. Lär dig mer om att konfigurera och ändra indexeringsprincip för automatisk indexering och bättre prestanda.
keywords: hur indexering fungerar, automatisk indexering, indexera database
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: d7cbeebff42bddd93cac35a0205d031a90bb4715
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056210"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hur fungerar dataindexeringen i Azure Cosmos DB?

Alla Azure Cosmos DB-data indexeras som standard. Även om många kunder är glada att låta Azure Cosmos DB automatiskt hantera alla aspekter av indexering, kan du ange en anpassad *indexeringspolicy* för samlingar i Azure Cosmos DB när den skapas. Indexering principer i Azure Cosmos DB är mer flexibla och kraftfullare än sekundärindex som erbjuds i andra databasplattformar. Du kan utforma och anpassa formen på indexet utan att offra flexibelt schema i Azure Cosmos DB. 

Om du vill veta hur indexering fungerar i Azure Cosmos DB, är det viktigt att veta att du kan göra detaljerade avvägningarna mellan Omkostnad för indexlagring, Skriv och frågedataflöde och frågekonsekvens när du hanterar indexeringsprincip.  

I följande videoklipp visar Azure Cosmos DB-Programhanteraren Andrew Liu automatisk indexering av funktioner och hur du finjustera och konfigurerar indexeringsprincipen på din Azure Cosmos DB-behållare för Azure Cosmos DB. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

I den här artikeln tar vi en titt Azure Cosmos DB indexeringsprinciper på hur du anpassar indexeringsprincip och associerade med de. 

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Hur åsidosätter jag egenskaperna för att inkludera eller exkludera från indexering?
* Hur kan jag konfigurera indexet för slutlig uppdateringar?
* Hur kan jag konfigurera indexering för att utföra ORDER BY- eller intervall frågor?
* Hur jag för att göra ändringar i en samling indexeringsprincip?
* Hur jag jämfört med lagring och prestanda för olika indexering principer?

## <a id="Indexing"></a> Cosmos DB-indexering

Syftet med databasindex är att leverera frågor i olika former och former med minsta resursförbrukning (t.ex. processor och indata/utdata) samtidigt som det ger bra dataflöde och låg fördröjning. Valet av rätt index för att fråga en databas kräver ofta mycket planerings- och experimentering. Den här metoden innebär en utmaning för schemalösa databaser där data stämmer inte överens med en strikt schema och utvecklas snabbt. 

När vi utformade undersystemet Cosmos DB för indexering, ange vi därför följande mål:

* Indexera dokument utan schema: undersystemet indexering inte kräver någon schemainformation eller göra några antaganden om schemat dokument.  

* Stöd för effektiv och omfattande hierarki- och relationella frågor: indexet har stöd för Cosmos DB-frågespråket effektivt, inklusive stöd för hierarkisk och relationsdata projektioner.  

* Stöd för konsekventa frågor in face of en fast mängd skrivningar: för hög skrivning dataflöde arbetsbelastningar med konsekventa frågor, indexet uppdateras stegvis, effektivt och online om en fast mängd skrivningar. Konsekvent indexuppdatering är avgörande för att leverera frågor på konsekvensnivå som du konfigurerade dokumenttjänsten.  

* Stöd för flera innehavare: den reserverade-baserade modellen för resursstyrning angivna över klienter, indexet uppdateringarna utförs inom budget systemresurser (processor, minne och indata/utdataåtgärder per sekund) allokera per replik.  

* Lagringseffektivitet: för kostnadseffektivitet, på disklagring arbetet med att indexet är begränsad och förutsägbara. Detta är viktigt eftersom Cosmos DB kan utvecklare göra kostnadsbaserad kompromisser mellan index arbetet i förhållande till frågeprestandan.  

## Anpassa indexeringsprincip i en samling <a id="CustomizingIndexingPolicy"></a>  
Du kan anpassa avvägningarna mellan lagring, skriva och frågeprestanda och frågekonsekvens genom att åsidosätta standardinställningen indexeringspolicy på en Azure Cosmos DB-samling. Du kan konfigurera följande aspekter:

* **Inkludera eller exkludera dokument och sökvägar till och från indexet**. Du kan exkludera eller inkludera specifika dokument i indexet när du infoga eller ersätta dokument i samlingen. Du kan också ta med eller undanta specifika JSON-egenskaper, även kallat *sökvägar*, som ska indexeras över dokument som ingår i ett index. Utbildningsvägar innefattar mönster med jokertecken.
* **Konfigurera olika typer av index**. Du kan ange vilken typ av index sökvägen som krävs för en samling för varje inkluderade sökväg. Du kan ange vilken typ av index baserat på den sökvägen data, förväntade fråga arbetsbelastning och numeriska/strängen ”precision”.
* **Konfigurera index update lägen**. Azure Cosmos DB stöder tre indexering lägen: konsekvent och Lazy, och ingenting alls. Du kan konfigurera indexering lägen via indexprincip på en Azure Cosmos DB-samling. 

Microsoft .NET-kodfragment visar hur du ställer in en anpassad indexeringsprincip när du skapar en samling. I det här exemplet ange vi en policy med ett intervallsindex för strängar och tal på den maximala precisionen. Du kan använda den här principen för att köra ORDER BY-frågor mot strängar.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> JSON-schema för indexeringsprincip ändras från och med lanseringen av REST API-version 2015-06-03. Med den versionen stöder JSON-schema för indexeringspolicy intervallet index mot strängar. .NET SDK 1.2.0 och Java, Python och Node.js-SDK: er 1.1.0 stöd för det nya principschemat. Tidigare versioner av SDK: N använder REST-API-version 2015-04-08. De stöder det tidigare schemat för indexering av principen.
> 
> Som standard indexerar Azure Cosmos DB alla egenskaper för anslutningssträngar i dokument konsekvent med ett Hash-index. Den indexerar alla numeriska egenskaper i dokument konsekvent med ett intervallsindex.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Anpassa indexeringsprincip i portalen

Du kan ändra indexprincip i en samling i Azure portal: 

1. Gå till ditt Azure Cosmos DB-konto i portalen och välj sedan din samling. 
2. Välj i den vänstra navigeringsmenyn **inställningar**, och välj sedan **Indexeringsprincip**. 
3. Under **Indexeringsprincip**, ändra indexprincip och väljer sedan **OK**. 

### Databasen indexering lägen <a id="indexing-modes"></a>  
Azure Cosmos DB stöder tre indexering lägen som du kan konfigurera via indexeringsprincip på en Azure Cosmos DB-samling: konsekvent och Lazy, och ingenting alls.

**Konsekvent**: om en Azure Cosmos DB-samling princip konsekvens frågor på en specifik Azure Cosmos DB-samling följer du samma konsekvensnivå som angetts för punkt-läsningar (stark, bunden utgång, session eller eventuell). Indexet har uppdaterats synkront som en del av dokumentet uppdateringen (Infoga, Ersätt, uppdatera och ta bort ett dokument i en Azure Cosmos DB-samling).

Konsekvent indexering stöder konsekventa frågor på bekostnad av en eventuell minskning i genomströmning för skrivning. Den här minskningen är en funktion av de unika sökvägarna som måste indexeras och ”konsekvensnivå”. Konsekvent indexering läge är utformat för ”skriva snabbt, fråga direkt” arbetsbelastningar.

**Lazy**: indexet uppdateras asynkront när ett Azure Cosmos DB-samling overksamt, det vill säga när samlingens dataflödeskapacitet inte fullt ut för att hantera användarförfrågningar.  Observera att du kan få inkonsekventa resultat eftersom data matas in och indexera långsamt. Det innebär att dina antal frågor eller specifika frågeresultat inte kanske konsekventa eller upprepningsbara vid den angivna tiden. 

Indexet är vanligtvis i catch-up läge med insamlade data. Med Lazy indexering, ändrar tid att live (TTL) resulterar i indexet som släppas och återskapas. Detta gör att antalet och fråga resultaten inkonsekvent under en viss tidsperiod. De flesta Azure Cosmos DB-konton bör använda konsekvent indexering läge.

**Ingen**: en samling som har en ingen indexläge har inget index som är kopplade till den. Detta används ofta om Azure Cosmos DB används som en nyckel / värde-lagring och dokument används endast av deras ID-egenskap. 

> [!NOTE]
> Om du konfigurerar indexeringsprincipen med eftersom ingen har att släppa alla befintliga indexet. Använd det här alternativet om din åtkomstmönster kräver endast ID eller självlänkar.
> 
> 

I följande tabell visas konsekvens för frågor som baseras på indexering läge (konsekvens och Lazy) som konfigurerats för samlingen och konsekvensnivå som angetts för query-fråga. Detta gäller för frågor genom att använda alla gränssnitt: REST API, SDK: er, eller inifrån lagrade procedurer och utlösare. 

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|
|---|---|---|
|Stark|Stark|Eventuell|
|Begränsad föråldring|Begränsad föråldring|Eventuell|
|Session|Session|Eventuell|
|Eventuell|Eventuell|Eventuell|

Azure Cosmos DB returneras ett fel för frågor som görs på samlingar som inte har något indexering läge. Frågor kan fortfarande köras som genomsökningar via den explicita **x-ms-documentdb-enable-genomsökning** huvud i REST API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Vissa frågefunktioner, som ORDER BY stöds inte som genomsökningar med **EnableScanInQuery**.

I följande tabell visas konsekvens för frågor som baseras på indexering läge (konsekvens Lazy och ingen) när **EnableScanInQuery** har angetts.

|Konsekvens|Indexering läge: konsekvent|Indexering läge: Lazy|Indexering läge: ingen|
|---|---|---|---|
|Stark|Stark|Eventuell|Stark|
|Begränsad föråldring|Begränsad föråldring|Eventuell|Begränsad föråldring|
|Session|Session|Eventuell|Session|
|Eventuell|Eventuell|Eventuell|Eventuell|

I följande exempel visar hur skapar ett Azure Cosmos DB-samling med hjälp av .NET SDK konsekvent indexering för alla dokument infogningar.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index sökvägar
Azure Cosmos DB-modeller JSON-dokument och index som träd. Du kan finjustera principer för sökvägar i trädet. Du kan välja sökvägarna att inkludera eller exkludera från att indexera dokument. Detta kan erbjuda bättre skrivprestanda och lagring med lägre index för scenarier där frågemönstren är kända i förväg.

Index sökvägar som börjar med root (/) och vanligtvis avslutas med det? jokertecken operator. Detta anger att det finns flera möjliga värden för prefixet. Till exempel för att hantera Välj * från familjer F var F.familyName = ”Andersen”, måste du inkludera ett index sökvägen för /familyName/? i den samlingen index principen.

Index sökvägar kan också använda den \* jokertecken operatör att ange beteendet för sökvägar rekursivt under prefixet. Till exempel/nyttolast / * kan användas för att undanta allt under egenskapen nyttolast från indexering.

Här följer vanliga mönster för att ange index sökvägar:

| Sökväg                | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Standardsökvägen för samlingen. Rekursiva och gäller för hela dokumentträdet.                                                                                                                                                                                                                                   |
| / prop /?             | Index-sökväg som krävs för att leverera frågor som liknar följande (med Hash- eller typer, respektive):<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                       |
| / prop / *             | Index sökväg för alla sökvägar under den angivna etiketten. Fungerar med följande frågor<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5<br><br>Välj från samlingen-c WHERE c.prop.subprop.nextprop = ”value”<br><br>Välj samling c ORDER BY c.prop         |
| / sammanställer / [] /?         | Index sökväg krävs för att hantera iteration och delta i frågor mot matriser av skalärer som [”a”, ”b”, ”c”]:<br><br>Välj tagg från taggen i collection.props var taggen = ”value”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props där tagga > 5                                                                         |
| /Props/ [] /subprop/? | Index sökväg krävs för att hantera iteration och JOIN-frågor mot matriser av objekt som [{subprop: ”a”}, {subprop: ”b”}]:<br><br>Välj tagg från taggen i collection.props var tag.subprop = ”value”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props var tag.subprop = ”value”                                  |
| / prop/subprop /?     | Index-sökväg som krävs för att leverera frågor (med Hash- eller typer, respektive):<br><br>Välj från samlingen-c WHERE c.prop.subprop = ”value”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> När du ställer in anpassat index sökvägar du behöver ange Standardregeln för indexering för trädet hela dokumentet som markerats med särskilda sökvägen ”/ *”. 
> 
> 

I följande exempel konfigureras en angiven sökväg med intervallsindex och anpassade precisionsvärdet 20 byte:

```
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
```

När du lägger till en sökväg för indexering, som både tal och strängar inom dessa sökvägar indexeras. Så även om du definierar indexering för endast strängar, lägger Azure Cosmos DB till standarddefinition för samt tal. Med andra ord Azure Cosmos DB har möjligheten för sökvägen undantag från indexeringsprincip, men skriva inte undantag från en angiven sökväg. Följande är ett exempel, Observera att det bara ett index har angetts för båda sökvägarna (sökväg = ”/ *” och sökväg = ”/\"attr1\"/”?) men antalet datatype läggs också till resultatet.

```
var indices = new[]{
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 }// <- note: only 1 index specified
                    },
                    Path =  "/*"
                },
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 } // <- note: only 1 index specified
                    },
                    Path =  "/\"attr1\"/?"
                }
            };...

            foreach (var index in indices)
            {
                documentCollection.IndexingPolicy.IncludedPaths.Add(index);
            }
```

Resultatet av skapandet av index:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        },
        {
            "path": "/\"attr\"/?",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        }
    ],
}
```

### <a name="index-data-types-kinds-and-precisions"></a>Index-datatyper, typer och Precision-datorerna
Du har flera alternativ när du konfigurerar indexeringsprincipen för en sökväg. Du kan ange en eller flera indexering definitioner för varje sökväg:

* **Datatypen**: sträng, nummer, punkt, Polygon eller LineString (kan innehålla endast en post per datatypen per sökväg).
* **Indexera typ**: Hash (likhetsfrågor), intervall (likhet, intervall eller ORDER BY-frågor) eller avstånds (rumsliga förfrågningar).
* **Precision**: för ett Hash-index, detta varierar från 1 till 8 för både strängar och siffror. Standardvärdet är 3. Det här värdet kan vara -1 (maximal precision) för ett intervall-index. Detta kan variera beroende finns mellan 1 och 100 (maximal precision) för sträng eller numeriska värden.

#### <a name="index-kind"></a>Index-typ
Azure Cosmos DB stöder Hash-index och intervallet index typer för varje sökväg som kan konfigureras för strängen eller talvärdet datatyper, eller båda.

* **Hash** stöder effektiv likhet och JOIN-frågor. De flesta användningar behöver Hash-index en högre precision än standardvärdet 3 byte. Datatypen kan vara en sträng eller en siffra.
* **Intervallet** stöder effektiv likhetsfrågor, omfångsfrågor (med hjälp av >, <>, =, < =,! =), och ORDER BY-frågor. ORDER By-frågor som standard även kräva maximala index precision (-1). Datatypen kan vara en sträng eller en siffra.

Azure Cosmos DB stöder också spatialindexet-typ för varje sökväg som kan anges för punkt, Polygon eller LineString-datatyper. Värdet på den angivna sökvägen måste vara ett giltigt GeoJSON-fragment som `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** stöder effektiv spatial (inom och avståndet) frågor. Datatypen kan vara punkt, Polygon eller LineString.

> [!NOTE]
> Azure Cosmos DB har stöd för automatisk indexering av Point och Polygon LineString-datatyper.
> 
> 

Här är de typer stöds index och exempel på frågor som de kan användas för att tillhandahålla:

| Index-typ | Beskrivning/användningsfall                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash-över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Hash över/sammanställer / [] /? (eller / eller/sammanställer /) kan användas för att effektivt hantera följande frågor:<br><br>Välj tagg från samlingen c JOIN-tagg i c.props var taggen = 5                                                                                                                       |
| Intervall      | Variera över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Variera över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen c<br><br>VAR ST_DISTANCE (c.prop, {”type”: ”pekar”, ”coordinates”: [0.0, 10.0]}) < 40<br><br>Välj från samlingen c där ST_WITHIN(c.prop, {"type": "Polygon",...})--med indexering för punkter som aktiverats<br><br>Välj från samlingen c där ST_WITHIN({"type": "Point",...}, c.prop)--med indexering för polygoner aktiverat              |

Som standard returneras ett fel för frågor med intervallet operatörer som > = om det finns inget intervallsindex (av valfri precision) att signalera att en sökning kan vara nödvändigt att betjäna frågan. Intervallfrågor kan utföras utan ett intervallsindex med hjälp av den **x-ms-documentdb-enable-genomsökning** huvud i REST API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Om det finns några andra filter i frågan att Azure Cosmos DB kan använda indexet för att filtrera mot, returneras inget fel.

Samma regler gäller för rumsliga förfrågningar. Som standard returneras ett fel för rumsliga förfrågningar om det finns inga spatialindexet och inga andra filter som kan hanteras från indexet. De kan utföras som en genomsökning med hjälp av **x-ms-documentdb-enable-genomsökning** eller **EnableScanInQuery**.

#### <a name="index-precision"></a>Index precision
Du kan använda indexet precision för att kompromissa mellan index storage overhead och frågeprestanda. För siffror rekommenderar vi använder standardkonfigurationen för precision-1 (max). Eftersom siffror är 8 byte i JSON, motsvarar detta en konfiguration av 8 byte. Välja ett lägre värde för noggrannhet på millisekunder, till exempel 1 och 7, index innebär det att värdena inom vissa intervall som mappas till samma-post. Därför kan du minska index lagringsutrymme, men Frågekörningen behöva bearbeta fler dokument. Därför kräver mycket större dataflöde i begäransenheter.

Indexkonfigurationen för precision har mer praktiska program med sträng-intervall. Eftersom strängar kan vara en godtycklig längd, kan valet av index precisionen påverka prestanda för frågor för sträng-intervallet. Det kan även påverka mängden index lagringsutrymme som krävs. Sträng intervallet index kan konfigureras med 1 och 100 eller -1 (max). Om du vill utföra ORDER BY-frågor mot strängegenskaper, måste du ange en noggrannhet på 1 för motsvarande sökvägar.

Spatialindex använda alltid standard index precisionen för alla typer (Point, LineString och Polygon). Standard index precisionen för rumsindex kan inte åsidosättas. 

I följande exempel visas hur du ökar precisionen för intervallet index i en samling med hjälp av .NET SDK. 

**Skapa en samling med ett anpassat index precision**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB Returnerar ett fel när en fråga använder ORDER BY men inte har en intervallsindex mot den efterfrågade sökvägen med den maximala precisionen. 
> 
> 

På samma sätt kan du helt utesluta sökvägar från indexering. I nästa exempel visas hur du undantar en hela avsnittet dokument (en *underträd*) från att indexera med hjälp av den \* jokertecken operator.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Delta och välja bort indexering
Du kan välja om du vill att samlingen som automatiskt indexerar alla dokument. Alla dokument som indexeras automatiskt som standard, men du kan inaktivera automatisk indexering. När är avstängd indexera dokument kan nås endast via sina egna länkar eller av frågor med hjälp av webbplatsen-ID.

Med automatisk indexering stängs av, kan du fortfarande selektivt lägga till endast specifika dokument till indexet. Däremot kan du lämna automatisk indexering för och selektivt vill undanta specifika dokument. Indexering på/av konfigurationer är användbara när du har endast en delmängd av dokument som ska efterfrågas.

I följande exempel visas hur du lägger till ett dokument uttryckligen med hjälp av den [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) och [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) egenskapen.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Ändra indexeringsprincip i en samling
I Azure Cosmos DB kan du ändra till indexprincip i en samling i farten. En ändring i indexeringspolicy på en Azure Cosmos DB-samling kan leda till en ändring i form av indexet. Påverkas sökvägarna som kan indexeras, deras precision och konsekvensmodell indexets själva. En ändring i indexeringspolicy effektivt kräver en omvandling av gamla index till ett nytt index. 

**Online-index omvandlingar**

![Så fungerar indexering – Azure Cosmos DB onlineindexgenerering omvandlingar](./media/indexing-policies/index-transformations.png)

Index transformeringar görs online. Det innebär att dokument som indexerats per gamla principen omvandlas effektivt per den nya principen *utan att påverka tillgängligheten för skrivning eller det etablerade dataflödet* i samlingen. Konsekvenskontroll av läsa och skriva åtgärder som utförs med hjälp av REST-API, SDK: er, eller inifrån lagrade procedurer och utlösare påverkas inte under index omvandling. 

Ändra indexeringsprincip är en asynkron åtgärd och hur lång tid att slutföra åtgärden beror på antal dokument, etablerade ru: er och storleken på dokument. Medan omindexering pågår, returnerar frågan inte alla matchande resultat om frågan använder det index som håller på att ändras och frågor som inte returnerar eventuella fel/fel. Medan omindexering pågår, är frågorna konsekvent oavsett indexering läge är konfigurerad (konsekvens eller Lazy). När indexet fortsätter omvandlingen är klar, du att se enhetliga resultat. Detta gäller även för frågor från alla gränssnitt: REST API, SDK: er, och inifrån lagrade procedurer och utlösare. Precis som med Lazy indexering, utförs omvandling av indexet asynkront i bakgrunden på replikerna med hjälp av ledig resurserna som är tillgängliga för en specifik replik. 

Index transformeringar görs också på plats. Azure Cosmos DB ha inte två kopior av index och Byt ut gamla indexet med den nya. Det innebär att inga ytterligare diskutrymme krävs eller konsumeras i dina samlingar medan index omvandlingar.

När du ändrar indexeringsprincip, tillämpas ändringar för att flytta från det gamla indexet till den nya främst baserat på indexering läge konfigurationer. Indexering läge konfigurationer spela en viktigare roll än de andra värdena som inkluderad/exkluderad sökvägar, index-typer och Precision-datorerna. 

Om din gamla och nya både principer använder konsekvent indexering, utför en online-index omvandling i Azure Cosmos DB. Du kan inte använda en annan indexering ändring som har konsekventa indexering läge medan transformeringen pågår. Du kan dock gå till Lazy eller ingen indexering läge under en transformering pågår: 

* När du flyttar till Lazy är principändringen index omedelbart verksam. Azure Cosmos DB startar återskapa indexet asynkront. 
* När du flyttar till ingen tas indexet bort omedelbart. Flytta till ingen är användbart när du vill avbryta en pågående omvandling och börja från början med en annan indexeringsprincip. 

Följande kodfragment visar hur du ändrar en samling indexeringsprincip från konsekvent indexering läge till Lazy indexering läge. Om du använder .NET SDK kan du sätta igång en ändring av indexering med hjälp av den nya **ReplaceDocumentCollectionAsync** metod.

**Ändra indexeringsprincip från konsekvens till Lazy**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Spåra förloppet för index-transformering**

Du kan spåra procent förloppet för transformeringen index till ett konsekvent index med hjälp av den **IndexTransformationProgress** svar egenskap från en **ReadDocumentCollectionAsync** anropa. Andra SDK: er och REST API, stöd för motsvarande egenskaper och metoder för att göra ändringar på principer för indexering. Du kan kontrollera status för ett index omvandling till ett konsekvent index genom att anropa **ReadDocumentCollectionAsync**: 

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
> * Den **IndexTransformationProgress** egenskapen gäller bara när du transformerar till ett konsekvent index. Använd den **ResourceResponse.LazyIndexingProgress** egenskapen för att spåra transformationer till ett Lazy-index.
> * Den **IndexTransformationProgress** och **LazyIndexingProgress** egenskaper fylls bara för en icke-partitionerad samling, det vill säga en samling som har skapats utan någon partitionsnyckel.
>

Du kan släppa indexet för en samling genom att flytta till ingen indexering läge. Det kan vara användbara när operational om du vill avbryta en pågående omvandling och starta sedan en ny omedelbart.

**Ta bort indexet för en samling**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

När blir du indexering principändringar till din Azure Cosmos DB-samlingar Här följer några vanliga användningsområden:

* Leverera enhetliga resultat under normal drift, men återgå till Lazy indexering läge under bulk dataimporter.
* Börja använda nya funktioner för indexering på din nuvarande Azure Cosmos DB-samlingar. Du kan till exempel använda geospatiala frågor, vilket kräver spatialindexet-typ eller ORDER BY / sträng intervallfrågor som kräver strängen intervallet index typ.
* Hand-Välj egenskaper som ska indexeras och ändra dem över tid.
* Justera indexering precision att förbättra frågeprestanda eller för att sänka förbrukad lagring.

> [!NOTE]
> Ändra indexeringsprincip med hjälp av **ReplaceDocumentCollectionAsync**, måste du använda version 1.3.0 eller en senare version av .NET SDK.
> 
> Se till att det finns tillräckligt med ledigt lagringsutrymme på samlingen för index transformering har slutförts har. Om samlingen når sin lagringskvot, har index-transformeringen pausats. Index omvandling återupptas automatiskt när lagringsutrymmet är tillgängligt, till exempel om du tar bort några dokument.
> 
> 

## <a name="performance-tuning"></a>Prestandajustering
SQL-API: er ger information om prestandavärden, till exempel index lagringsutrymme och dataflöde kostnaden (begäransenheter) för varje åtgärd. Du kan använda den här informationen för att jämföra olika principer för indexering, och för prestandajustering.

Du kan kontrollera lagringskvoten och användning av en samling genom att köra en **HEAD** eller **hämta** begäran mot samlingen resursen. Granska sedan de **x-ms-begäran-quota** och **x-ms-begäran-användning** rubriker. I .NET-SDK i [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) och [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) egenskaper i [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) innehålla dessa motsvarande värden.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Att mäta arbetet med att indexering för varje skrivåtgärd (skapa, uppdatera eller ta bort), granska de **x-ms-begäran-kostnad** rubrik (eller motsvarande [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) -egenskapen i [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) i .NET SDK) att mäta antalet enheter för programbegäran som förbrukas av de här åtgärderna.

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

## <a name="changes-to-the-indexing-policy-specification"></a>Ändringar av onlineindex principspecifikationen
En ändring i schemat för indexeringspolicy introducerades 7 juli 2015 med REST API-version 2015-06-03. Motsvarande klasser i SDK-versioner har nya implementeringar så att den matchar schemat. 

Följande ändringar har införts i JSON-specifikationen:

* Indexeringspolicy stöder intervallet index för strängar.
* Varje sökväg kan ha flera index-definitioner. Det kan ha en för varje datatyp.
* Indexering precision stöder 1 och 8 för tal, 1 och 100 för strängar och -1 (maximal precision).
* Sökvägssegment kräver inte ett citattecken för att undvika varje sökväg. Du kan till exempel lägga till en sökväg för   **/rubrik /?** i stället för **/ ”title” /?**.
* Rotsökvägen som representerar ”alla sökvägar” kan representeras **/ \*** (förutom **/**).

Om du har kod som etablerar samlingar med en anpassad indexeringsprincip som skrivits med .NET SDK version 1.1.0 eller tidigare måste du ändra din programkod för att hantera dessa ändringar för att flytta till SDK-version 1.2.0 eller senare. Om du inte har kod som konfigurerar indexeringsprincip eller om du planerar att fortsätta att använda en tidigare version av SDK: N, inga ändringar krävs.

Här är ett exempel på en anpassad indexeringsprincip som skrivits med hjälp av REST API-version 2015-06-03, följt av samma indexeringsprincipen skrivna med hjälp av den tidigare REST-API-version 2015-04-08 en praktisk jämförelse.

**Aktuella indexeringspolicy JSON (REST API-version 2015-06-03)**

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


**Tidigare indexeringspolicy JSON (REST API-version 2015-04-08)**

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
För exemplen på principen index och vill veta mer om Azure Cosmos DB-frågespråk, se följande länkar:

* [SQL API .NET index management-kodexempel](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Åtgärder för insamling av SQL API-REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Fråga med SQL](sql-api-sql-query.md)

