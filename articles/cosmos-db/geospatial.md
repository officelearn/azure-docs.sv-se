---
title: Arbeta med geospatiala data i Azure Cosmos DB SQL API-konto
description: Förstå hur du skapar, index- och frågar rumsliga objekt med Azure Cosmos DB och SQL API.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: sngun
ms.openlocfilehash: 29bb495bbbd56ab39964b34db35fb4d222a60179
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074703"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Använda geospatiala och GeoJSON platsdata med Azure Cosmos DB SQL API-konto

Den här artikeln ger en introduktion till geospatiala funktionerna i Azure Cosmos DB. Lagra och komma åt geospatiala data för närvarande stöds av Cosmos DB SQL API-konton. När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Hur jag för att lagra rumsliga data i Azure Cosmos DB?
* Hur kan jag läsa geospatiala data i Azure Cosmos DB i SQL och LINQ?
* Hur jag för att aktivera eller inaktivera rumslig indexering i Azure Cosmos DB?

Den här artikeln visar hur du arbetar med rumsliga data med SQL API. Se den här [GitHub-projektet](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) för kodexempel.

## <a name="introduction-to-spatial-data"></a>Introduktion till spatialdata
Spatialdata beskriver position och formen på objekt i utrymmet. I de flesta program motsvarar dessa objekt på jorden och geospatiala data. Rumsliga data kan användas för att representera platsen för en person, en plats i närheten eller ens en stad eller en sjö gränser. Vanliga användningsområden ofta omfattar närhetsförfrågningar, till exempel, ”hitta alla kaféer nära min aktuella plats”. 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB har stöd för indexering och frågor efter geospatiala punkt data som har representeras med hjälp av den [GeoJSON-specifikationen](https://tools.ietf.org/html/rfc7946). GeoJSON-datastrukturer är alltid giltig JSON-objekt, så att de kan lagras och frågor med Azure Cosmos DB utan några särskilda verktyg och bibliotek. Azure Cosmos DB SDK: er ger hjälpklasser och metoder som gör det enkelt att arbeta med spatialdata. 

### <a name="points-linestrings-and-polygons"></a>Punkter, LineStrings och polygoner
En **punkt** anger en enda plats i utrymmet. I geospatiala data motsvarar en punkt den exakta platsen, vilket kan vara en gatuadress en LIVSMEDELSBUTIK, en kiosk, en bil eller en stad.  En punkt representeras i GeoJSON (och Azure Cosmos DB) med hjälp av dess koordinat par eller longitud och latitud. Här är ett exempel på JSON för en punkt.

**Punkter i Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON-specifikationen anger longitud första och latitud andra. Som i andra program för mappning, longitud och latitud är vinklar och representeras i grader. Longitudvärden mäts från nollmeridianen och är mellan-180 grader och 180.0 grader, och värdena för latitud mäts från ekvatorn och är mellan-90.0 grader och 90.0 grader. 
> 
> Azure Cosmos DB tolkar koordinater framställd per referenssystem WGS 84. Nedan finns mer information om koordinaten referenssystem.
> 
> 

Detta kan vara inbäddad i ett Azure Cosmos DB-dokument som visas i det här exemplet på en profil som innehåller platsdata:

**Använd profil med platsen som lagras i Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

Förutom punkter stöder också GeoJSON LineStrings och polygoner. **LineStrings** representerar en serie med två eller fler punkter i utrymme och linjesegment som förenar dem. I geospatiala data användas LineStrings brukar för att representera motorvägar eller vattendrag. En **Polygon** är en uppsättning anslutna punkter som utgör en stängd LineString. Polygoner användas brukar för att representera naturlig buskmarker som sjöar eller politisk jurisdiktioner som orter och delstater. Här är ett exempel på en Polygon i Azure Cosmos DB. 

**Polygoner i GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON-specifikationen kräver att för giltiga polygoner senaste koordinaten paret som anges ska vara samma som först som skapar en sluten form.
> 
> Punkter inom en Polygon måste anges i motsols ordning. En Polygon som angetts i medurs ordning representerar inversen till regionen i den.
> 
> 

Förutom Point, LineString och Polygon GeoJSON också anger representation för gruppering flera geospatiala platser, samt hur du associerar godtyckliga egenskaper med geoplats som en **funktionen**. Eftersom de här objekten är giltig JSON, kan de alla lagras och bearbetas i Azure Cosmos DB. Azure Cosmos DB stöder dock endast automatisk indexering av punkter.

### <a name="coordinate-reference-systems"></a>Samordna referenssystem
Eftersom formen på jorden är oregelbunden, representeras koordinaterna för geospatiala data i många koordinaten referenssystem (CRS), var och en med sin egen bildrutor of referens och måttenheter. Den ”nationella rutnät för Storbritannien” är till exempel en referenssystem stämmer för Storbritannien, men inte utanför den. 

Den mest populära CRS används idag är världen geodetiskt System [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Använd WGS 84 GPS-enheter och många mappning tjänster, inklusive Google Maps och Bing Maps API: er. Azure Cosmos DB har stöd för indexering och frågor efter geospatiala data med hjälp av det WGS 84 datoriserade. 

## <a name="creating-documents-with-spatial-data"></a>Skapa dokument med rumsliga data
När du skapar dokument som innehåller GeoJSON-värden, indexeras de automatiskt med en spatialindexet i enlighet med indexprincip i behållaren. Om du arbetar med ett Azure Cosmos DB SDK på ett dynamiskt skrivna språk som Python eller Node.js, måste du skapa giltiga GeoJSON.

**Skapa dokument med geospatiala data i Node.js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Om du arbetar med SQL-API: er kan du använda den `Point` och `Polygon` klasserna inom den `Microsoft.Azure.Documents.Spatial` namnområdet för att bädda in platsinformation i dina programobjekt. De här klasserna underlätta serialisering och deserialisering av spatialdata i GeoJSON.

**Skapa dokument med geospatiala data i .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Om du inte har nödvändig information för latitud och longitud, men har fysiska adresser eller platsnamnet som ort eller land, kan du söka efter de faktiska koordinaterna med hjälp av en geokodningstjänst som Bing Maps REST-tjänster. Läs mer om Bing Maps-geokodning [här](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Frågar rumsliga typer
Nu när vi har tagit en titt på hur du infogar geospatiala data, låt oss ta en titt på hur du frågar efter dessa data med Azure Cosmos DB med SQL och LINQ.

### <a name="spatial-sql-built-in-functions"></a>Spatial inbyggda SQL-funktioner
Azure Cosmos DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatiala frågor. Mer information om en fullständig uppsättning med inbyggda funktioner i SQL-språket finns i [fråga Azure Cosmos DB](how-to-sql-query.md).

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger huruvida det första GeoJSON-objektet (Point, Polygon eller LineString) finns i det andra GeoJSON-objektet (Point, Polygon eller LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger huruvida de två angivna GeoJSON-objekten (Point, Polygon eller LineString) överlappar varandra.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt, och orsaken som ett strängvärde om det är ogiltigt.</td>
</tr>
</table>

Spatiella funktioner kan användas för att köra närhetsfrågor mot rumsliga data. Här är till exempel en fråga som returnerar alla family dokument som ligger inom 30 km för den angivna platsen med hjälp av den inbyggda funktionen ST_DISTANCE. 

**Fråga**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Om du inkluderar rumslig indexering i indexprincip, sedan hanteras ”avståndet-frågor” effektivt via indexet. Mer information om rumslig indexering, finns i avsnittet nedan. Om du inte har en spatialindexet för de angivna sökvägarna, kan du fortfarande utföra rumsliga förfrågningar genom att ange `x-ms-documentdb-query-enable-scan` begärandehuvudet med värdet ”true”. I .NET, detta kan göras genom att skicka det valfria **FeedOptions** argumentet för frågor med [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) inställd på Sant. 

ST_WITHIN kan användas för att kontrollera om en punkten befinner sig inom en Polygon. Polygoner används ofta för att representera gränser som postnummer, tillstånd gränser eller naturlig buskmarker. Igen om du inkluderar rumslig indexering i indexprincip, sedan ”i” frågor hanteras effektivt via indexet. 

Polygon argument i ST_WITHIN kan innehålla endast en ringsignal, det vill säga polygonerna får inte innehålla tomrum i dem. 

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Results**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Liknar hur inkompatibla typer arbete i Azure Cosmos DB-fråga om plats-värde som anges i antingen argumentet är skadad eller ogiltig sedan värdet **odefinierat** och utvärderade dokumentet som ska hoppas över från resultatet av frågan. Om din fråga returnerar inga resultat, kör du ST_ISVALIDDETAILED till felsökning varför spatial typ är ogiltig.     
> 
> 

Azure Cosmos DB stöder också utföra inverterade frågor, det vill säga du indexera polygoner eller rader i Azure Cosmos DB och sedan fråga efter de områden som innehåller en viss tidpunkt. Det här mönstret används ofta i logistik identifiera, till exempel när en lastbil anländer till eller lämnar en avsedda området. 

**Fråga**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Results**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID och ST_ISVALIDDETAILED kan användas för att kontrollera om en spatialobjektet är giltig. Till exempel kontrollerar följande fråga giltigheten för en tidpunkt med en out of latitudvärdet för intervall (-132.8). ST_ISVALID returnerar bara ett booleskt värde och ST_ISVALIDDETAILED returnerar booleskt och en sträng som innehåller orsaken till varför den är ogiltiga.

** Fråga **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Dessa funktioner kan också användas för att verifiera polygoner. Till exempel, använda här vi ST_ISVALIDDETAILED för att validera en Polygon som inte är stängd. 

**Fråga**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Results**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ-frågor i .NET SDK
SQL .NET SDK även providers stub-metoder `Distance()` och `Within()` för användning i LINQ-uttryck. SQL LINQ-providern översätter den här metoden anrop till de motsvarande inbyggda SQL-funktionsanrop (ST_DISTANCE och ST_WITHIN respektive). 

Här är ett exempel på en LINQ-fråga som söker efter alla dokument i Azure Cosmos DB-samling vars värde för ”plats” är inom en radie på 30 km från varandra i den angivna peka med hjälp av LINQ.

**LINQ-frågan för avståndet**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Här är på samma sätt kan en fråga för att hitta alla dokument vars ”plats” ligger inom den angivna box/polygonen. 

**LINQ fråga efter inom**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Nu när vi har tagit en titt på hur du frågar dokument med hjälp av LINQ och SQL, låt oss ta en titt på hur du konfigurerar Azure Cosmos DB för rumslig indexering.

## <a name="indexing"></a>Indexering
Som vi beskrivs i den [Schema oberoende indexering med Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) dokumentet, vi utformade Azure Cosmos DB: s databasmotor är verkligen schemaoberoende och ange förstklassigt stöd för JSON. Skrivoptimerad databasmotor hos Azure Cosmos DB tolka rumsliga data (punkter, polygoner och rader) som representeras i GeoJSON-standard.

Kortfattat, geometrin projected från geodetisk koordinater till en 2D-plan och uppdelad progressivt i celler med hjälp av en **quadtree**. De här cellerna är mappade till 1D baserat på platsen för cellen i en **Hilbert utrymme fylls kurvan**, som bevarar ort punkter. Dessutom när platsdata är indexerad, genomgår en process som kallas **tessellation**, det vill säga alla celler som korsar en plats identifieras och lagras som nycklar i Azure Cosmos DB-index. När en fråga körs argument som punkter och polygoner är också fasetterade för att extrahera de relevanta cellintervall ID och sedan används för att hämta data från indexet.

Om du anger en indexprincip som innehåller spatialindexet för / * (alla sökvägar), och sedan alla punkter i samlingen indexeras för effektiv rumsliga förfrågningar (ST_WITHIN och ST_DISTANCE). Spatialindex inte ha ett precisionsvärde och alltid använda ett standardvärde för precision.

> [!NOTE]
> Azure Cosmos DB stöder automatisk indexering av punkter och polygoner LineStrings
> 
> 

Följande JSON-kodfragmentet visar en indexeringsprincip med rumslig indexering aktiverat, det vill säga, indexet helst GeoJSON hittades i dokument för spatial frågor. Om du ändrar den indexprincip som med Azure portal kan ange du följande JSON för indexeringsprincip att aktivera spatial indexering för din samling.

**Samling indexering princip JSON med Spatial aktiverad för punkter och polygoner**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Här är ett kodfragment i .NET som visar hur du skapar en samling med rumslig indexering aktiveras för alla sökvägar som innehåller punkter. 

**Skapa en samling med rumslig indexering**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Och här är hur du kan ändra en befintlig samling för att kunna utnyttja rumslig indexering för alla frågor som lagras i dokument.

**Ändra en befintlig samling med rumslig indexering**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Om platsen GeoJSON värde inom dokumentet är skadad eller ogiltig, få sedan inte indexera för spatial frågor. Du kan validera plats värden med hjälp av ST_ISVALID och ST_ISVALIDDETAILED.
> 
> Om din samling definition innehåller en partitionsnyckel, rapporteras indexering omvandling förloppet inte. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Bredvid nu när du har lärt dig hur du kommer igång med geospatialt stöd i Azure Cosmos DB, kan du:

* Börja koda med den [geospatiala .NET-kodexempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Praktiska övningar och få med geospatiala frågor på den [Frågespelplan för Azure Cosmos DB](https://www.documentdb.com/sql/demo#geospatial)
* Läs mer om [Azure Cosmos DB-fråga](how-to-sql-query.md)
* Läs mer om [indexera Azure Cosmos DB-principer](index-policy.md)

