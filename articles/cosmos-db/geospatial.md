---
title: Arbeta med geospatiala data i Azure Cosmos DB | Microsoft Docs
description: "Förstå hur du skapar, index- och fråga spatial objekt med Azure Cosmos DB och SQL-API."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e778f4a9b7ec4935d53eb335462f3c414ff99cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Arbeta med geospatial och GeoJSON platsdata i Azure Cosmos DB
Den här artikeln ger en introduktion till funktionen geospatiala i [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). När du har läst detta, kommer du att kunna besvara följande frågor:

* Hur lagrar spatial data i Azure Cosmos DB?
* Hur kan jag fråga geospatiala data i Azure Cosmos-databasen i SQL- och LINQ?
* Hur jag för att aktivera eller inaktivera spatial indexering i Azure Cosmos DB?

Den här artikeln visar hur du arbetar med spatialdata med SQL-API. Se den här [GitHub projekt](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) för kodexempel.

## <a name="introduction-to-spatial-data"></a>Introduktion till spatialdata
Spatialdata beskriver position och form av objekt i utrymmet. I de flesta program motsvarar dessa objekt på jorden och geospatiala data. Spatialdata kan användas för att representera platsen där en person, en intressant plats eller på en stad eller ett lake kolumnrubrikens. Vanliga användningsområden omfattar närhet frågor, till exempel ofta, ”hitta alla kaféer nära mitt aktuella plats”. 

### <a name="geojson"></a>GeoJSON
Azure Cosmos-DB har stöd för indexering och frågar geospatiala punkt data som har representeras med hjälp av den [GeoJSON-specifikationen](https://tools.ietf.org/html/rfc7946). GeoJSON-datastrukturer är alltid giltig JSON-objekt så att de kan lagras och efterfrågas med hjälp av Azure Cosmos DB utan särskilda verktyg eller bibliotek. Azure Cosmos DB-SDK: er ger hjälpprogramklasser och metoder som gör det lättare att arbeta med spatialdata. 

### <a name="points-linestrings-and-polygons"></a>Punkter, LineStrings och polygoner
En **punkt** anger en enda plats i utrymme. I geospatiala data representerar en punkt den exakta platsen, vilket kan vara en gatuadress i en affären, en kiosk, en bil, eller en stad.  En punkt representeras i GeoJSON (och Azure Cosmos DB) med hjälp av dess koordinat par eller longitud och latitud. Här är ett exempel JSON för en punkt.

**Punkter i Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON-specifikationen anger longitud första och latitud andra. Precis som i andra mappning program longitud och latitud är vinklar och representeras i grader. Longitudvärden mäts från nollmeridianen och mellan-180 grader och 180.0 grader och latitudvärden mäts från ekvatorn och är mellan-90.0 grader och 90.0 grader. 
> 
> Azure Cosmos-DB tolkar koordinater som representeras per referenssystem WGS 84. Se nedan för mer information om koordinaten referenssystem.
> 
> 

Detta kan vara inbäddat i ett Azure DB som Cosmos-dokument som visas i det här exemplet på en profil som innehåller platsdata:

**Använd profil med platsen lagras i Azure Cosmos DB**

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

Förutom punkterna stöder också GeoJSON LineStrings och polygoner. **LineStrings** representerar en serie av två eller flera punkter i utrymme och linjesegment som ansluter dem. Geospatiala data, är LineStrings ofta används för att representera landsvägar eller floder. En **Polygon** är en gräns för anslutna pekar som utgör en stängd LineString. Polygoner används ofta för att representera fysiska formationer som sjöar eller politiska jurisdiktioner som orter och tillstånd. Här är ett exempel på en Polygon i Azure Cosmos-databasen. 

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
> Specifikationen GeoJSON kräver att för giltig polygoner senaste koordinaten paret som angetts ska vara samma som först som skapar en sluten form.
> 
> Punkter i en Polygon måste anges i följd medurs ordning. En Polygon som angetts i medurs ordning representerar inversen till regionen i den.
> 
> 

Förutom Point, LineString och Polygon anger GeoJSON också representation för hur du vill gruppera flera geospatiala platser, samt hur du associerar godtycklig egenskaper med geolokalisering som en **funktionen**. Eftersom de här objekten är giltig JSON, kan de alla lagras och behandlas i Azure Cosmos-databasen. Men Azure Cosmos DB endast stöd för automatisk indexering av punkter.

### <a name="coordinate-reference-systems"></a>Samordna referenssystem
Eftersom formen jordens oregelbundna representeras koordinater geospatiala data i många koordinaten referenssystem (CR), var och en med sin egen ramar på referens- och måttenheter. Den ”National rutnät av Storbritannien” är exempelvis referenssystem är mycket noggrann för Storbritannien men inte utanför den. 

Den mest populära CR används idag är World geodetiskt System [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Använd WGS 84 GPS-enheter och tjänster för många mappning inklusive Google kartor och Bing Maps API: er. Azure Cosmos-DB har stöd för indexering och frågar geospatiala data med hjälp av WGS 84-CR endast. 

## <a name="creating-documents-with-spatial-data"></a>Skapa dokument med spatialdata
När du skapar dokument som innehåller GeoJSON värden indexeras de automatiskt med ett spatial index i enlighet med indexprincip i mängden. Om du arbetar med en Azure Cosmos DB SDK i ett dynamiskt skrivna språk som Python eller Node.js, måste du skapa giltiga GeoJSON.

**Skapa ett dokument med geospatiala data i Node.js**

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

Om du arbetar med SQL-API: er, kan du använda den `Point` och `Polygon` klasserna i den `Microsoft.Azure.Documents.Spatial` namnområde att bädda in platsinformation i programobjekt. De här klasserna underlätta serialisering och deserialisering av spatialdata i GeoJSON.

**Skapa ett dokument med geospatiala data i .NET**

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

Om du inte har nödvändig information för latitud och longitud, men har fysiska adresser eller platsnamnet som stad eller ett land, kan du söka efter faktiska koordinaterna med hjälp av en tjänst för geokodning som Bing Maps REST-tjänster. Mer information om Bing Maps geokodning [här](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Fråga spatialtyper
Nu när vi har valt en titt på hur du infogar geospatiala data ska vi titta på hur man frågar dessa data med Azure Cosmos-databasen med SQL och LINQ.

### <a name="spatial-sql-built-in-functions"></a>Spatial inbyggda SQL-funktioner
Azure Cosmos-DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatial frågor. Mer information om en fullständig uppsättning med inbyggda funktioner i SQL-språket finns [frågan Azure Cosmos DB](sql-api-sql-query.md).

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Returnerar avståndet mellan de två GeoJSON punkt, Polygon eller LineString-uttrycken.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om det första GeoJSON-objektet (Point, LineString eller Polygon) är i andra GeoJSON-objektet (Point, LineString eller Polygon).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om de två angivna GeoJSON-objekt (Point, LineString eller Polygon) intersect.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerar ett booleskt värde som anger om det angivna uttrycket GeoJSON punkt, Polygon eller LineString är giltig.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerar ett JSON-värde som innehåller ett booleskt värde värdet om det angivna uttrycket för GeoJSON punkt, Polygon eller LineString är giltiga och ogiltiga, dessutom orsak som ett strängvärde.</td>
</tr>
</table>

Spatial funktioner kan användas för att utföra närhet frågor mot spatialdata. Här är till exempel en fråga som returnerar alla family dokument som ligger inom 30 km för den angivna platsen med hjälp av funktionen ST_DISTANCE inbyggda. 

**Fråga**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultat**

    [{
      "id": "WakefieldFamily"
    }]

Om du inkluderar spatial indexering i indexprincip, sedan hanteras ”avståndet frågor” effektivt via index. Se avsnittet nedan för mer information om spatial indexering. Om du inte har ett spatial index för angiven sökväg, kan du fortfarande utföra spatial frågor genom att ange `x-ms-documentdb-query-enable-scan` huvudet i begäran med värdet ”true”. I .NET, detta kan göras genom att skicka det valfria **FeedOptions** argumentet för frågor med [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) inställd på true. 

ST_WITHIN kan användas för att kontrollera om en punkten befinner sig inom en Polygon. Polygoner används ofta för att representera gränser som postnummer, tillstånd gränser eller fysiska konstruktioner. Igen om du inkluderar spatial indexering i indexprincip, sedan ”i” frågor hanteras effektivt via index. 

Polygon argument i ST_WITHIN kan innehålla endast en enkel signal, det vill säga polygonerna får inte innehålla tomrum i dem. 

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultat**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Liknar hur inkompatibla typer arbete i Azure Cosmos DB-fråga om plats-värdet som angetts i antingen argumentet är skadad eller ogiltig sedan värdet **Odefinierad** och utvärderade dokumentet ska hoppas över från resultatet av frågan. Om frågan returnerar inga resultat, kör du ST_ISVALIDDETAILED till debug varför spatial typ är ogiltig.     
> 
> 

Azure Cosmos-DB stöder också utföra omvända frågor, det vill säga kan du indexera polygoner eller rader i Azure Cosmos DB och sedan fråga efter områden som innehåller en angiven punkt. Det här mönstret används ofta i logistik att till exempel identifiera när en lastbil anländer till eller lämnar avsedda området. 

**Fråga**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultat**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID och ST_ISVALIDDETAILED kan användas för att kontrollera om en Rumsobjektet är giltigt. Till exempel kontrollerar följande fråga giltigheten för en plats med en out-of latitud intervall (-132.8). ST_ISVALID returnerar bara ett booleskt värde och ST_ISVALIDDETAILED returnerar booleskt och en sträng som innehåller orsaken till varför den är ogiltiga.

** Fråga **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultat**

    [{
      "$1": false
    }]

Dessa funktioner kan också användas för att validera polygoner. Till exempel använder här vi ST_ISVALIDDETAILED för att validera en Polygon som inte är stängd. 

**Fråga**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultat**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ-frågor skickas i .NET SDK
SQL .NET SDK också providers stub-metoder `Distance()` och `Within()` för användning i LINQ-uttryck. SQL-LINQ-providern översätter till motsvarande SQL inbyggda funktionsanropen metodanrop av (ST_DISTANCE och ST_WITHIN respektive). 

Här är ett exempel på en LINQ-fråga som hittar alla dokument i Azure Cosmos DB samlingen vars ”plats”-värdet är inom en radie på 30 km i den angivna peka med hjälp av LINQ.

**LINQ-fråga för avstånd**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Här är på samma sätt kan en fråga för att söka efter alla dokument som ”plats” ligger inom den angivna ruta/polygonen. 

**LINQ-fråga efter inom**

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


Nu när vi har valt en titt på hur man frågar dokument med hjälp av LINQ och SQL, låt oss ta en titt på hur du konfigurerar Azure Cosmos DB för spatial indexering.

## <a name="indexing"></a>Indexering
Som det beskrivs i den [Schema oberoende indexering med Azure Cosmos DB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) dokumentet, vi utformat Azure Cosmos DB databasmotorn ska verkligen schema oberoende och ge förstklassigt stöd för JSON. Databasmotorn skrivåtgärder som är optimerade för Azure Cosmos DB förstår internt spatial data (poäng, polygoner och rader) som representeras i GeoJSON-standard.

I kort sagt kan geometrin planerat från geodetisk koordinater till ett 2D-plan och progressivt indelat i celler med hjälp av en **quadtree**. De här cellerna mappas till 1D baserat på platsen för en cell i en **Hilbert utrymme fylla kurvan**, som bevarar ort punkter. Dessutom när lokaliseringsuppgifter indexeras går via en process som kallas **tessellation**, det vill säga alla celler som korsar en plats identifieras och lagras som nycklar i Azure DB som Cosmos-index. Frågan samtidigt är argument som points och polygoner också fasetterade extrahera relevanta cell ID-intervall och används för att hämta data från indexet.

Om du anger en indexprincip som innehåller rumsindexet för / * (alla sökvägar) och sedan alla punkter hittades i samlingen indexeras för effektiv spatial frågor (ST_WITHIN och ST_DISTANCE). Rumsindex inte har Precisionvärdet och alltid använda ett standardvärde för precision.

> [!NOTE]
> Azure Cosmos-DB har stöd för automatisk indexering av punkter och polygoner LineStrings
> 
> 

Följande JSON-fragment visas en indexprincip med spatial indexering som är aktiverad, index helst GeoJSON hittades inom dokument för spatial frågor. Om du ändrar den indexprincip som använder Azure portal, kan du ange följande JSON för indexprincip att aktivera spatial indexering på din samling.

**Samlingen indexering princip-JSON med Spatial aktiverad för punkter och polygoner**

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

Här är ett kodfragment i .NET som visar hur du skapar en samling med spatial indexering aktiverat för alla sökvägar som innehåller punkter. 

**Skapa en samling med spatial indexering**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Här är hur du ändrar en befintlig samling om du vill dra nytta av spatial indexering alla frågor som lagras i dokument.

**Ändra en befintlig samling med spatial indexering**

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
> Om platsen GeoJSON värde inom dokumentet är skadat eller ogiltigt, kommer sedan den inte hämta indexeras för spatial frågor. Du kan verifiera plats värden med hjälp av ST_ISVALID och ST_ISVALIDDETAILED.
> 
> Om din samling definition innehåller en partitionsnyckel, rapporteras indexering omvandling förloppet inte. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Nolearned som du har fått kännedom om hur du kommer igång med geospatial stöd i Azure Cosmos-databasen, kan du:

* Börja koda med den [geospatiala .NET kodexempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Hämta händerna med geospatial fråga på den [Azure Cosmos DB Query Playground](http://www.documentdb.com/sql/demo#geospatial)
* Lär dig mer om [Azure Cosmos DB-fråga](sql-api-sql-query.md)
* Lär dig mer om [Azure Cosmos DB indexering principer](indexing-policies.md)

