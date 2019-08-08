---
title: Fält mappningar för automatisk indexering med indexerare – Azure Search
description: Konfigurera fält mappningar för Azure Search indexeraren för att redovisa skillnader i fält namn och data representationer.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 771a6e413cd08a338da41c09cd6a0da35e28e5e4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840649"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Fält mappningar och transformeringar med hjälp av Azure Search indexerare

När du använder Azure Search indexerare ser du ibland till att indata inte stämmer överens med schemat för ditt mål index. I dessa fall kan du använda **fält mappningar** för att ändra form på dina data under indexerings processen.

Några situationer där fält mappningar är användbara:

* Data källan har ett fält med namnet `_id`, men Azure Search tillåter inte fält namn som börjar med ett under streck. Med en fält mappning kan du effektivt byta namn på ett fält.
* Du vill fylla i flera fält i indexet från samma data käll data. Du kanske till exempel vill använda olika analys verktyg för dessa fält.
* Du vill fylla i ett index fält med data från fler än en data källa och data källorna använder olika fält namn.
* Du måste base64 koda eller avkoda dina data. Fält mappningar har stöd för flera **mappnings funktioner**, inklusive funktioner för base64-kodning och avkodning.

> [!NOTE]
> Fält mappnings funktionen i Azure Search indexerare är ett enkelt sätt att mappa data fält till index fält, med några alternativ för data konvertering. Mer komplexa data kan kräva för bearbetning för att forma om till ett formulär som är enkelt att indexera.
>
> Microsoft Azure Data Factory är en kraftfull molnbaserad lösning för import och omvandling av data. Du kan också skriva kod för att transformera källdata före indexering. Kod exempel finns i [modell Relations data](search-example-adventureworks-modeling.md) och [modeller på flera nivåer](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Konfigurera fält mappningar

En fält mappning består av tre delar:

1. En `sourceFieldName`, som representerar ett fält i data källan. Den här egenskapen är obligatorisk.
2. En valfri `targetFieldName`, som representerar ett fält i Sök indexet. Om det utelämnas används samma namn som i data källan.
3. En valfri `mappingFunction`, som kan transformera dina data med hjälp av en av flera fördefinierade funktioner. Den fullständiga listan över funktioner finns [nedan](#mappingFunctions).

Fält mappningar läggs till `fieldMappings` i matrisen för index definition.

## <a name="map-fields-using-the-rest-api"></a>Mappa fält med hjälp av REST API

Du kan lägga till fält mappningar när du skapar en ny indexerare med hjälp av API-förfrågan [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . Du kan hantera fält mappningar för en befintlig indexerare med hjälp av API-begäran [Uppdatera indexerare](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Så här mappar du till exempel ett käll fält till ett målfält med ett annat namn:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Det går att referera till ett käll fält i flera fält mappningar. I följande exempel visas hur du kopierar samma käll fält till två olika index fält med "förgrening" i ett fält:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search använder SKIFT läges okänslig jämförelse för att matcha fält-och funktions namn i fält mappningar. Detta är praktiskt (du behöver inte hämta alla Skift läges rättigheter), men det innebär att data källan eller indexet inte kan ha fält som bara skiljer sig från versaler.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mappa fält med hjälp av .NET SDK

Du definierar fält mappningar i .NET SDK med klassen [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , som har egenskaperna `SourceFieldName` och `TargetFieldName`, och en valfri `MappingFunction` referens.

Du kan ange fält mappningar när du konstruerar indexeraren eller senare genom att `Indexer.FieldMappings` ange egenskapen direkt.

I följande C# exempel anges fält mappningar när du skapar en indexerare.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fält mappnings funktioner

En fält mappnings funktion omvandlar innehållet i ett fält innan det lagras i indexet. Följande mappnings funktioner stöds för närvarande:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>funktionen base64Encode

Utför *URL-säker base64-* kodning för Indatasträngen. Förutsätter att indatatypen är UTF-8-kodad.

#### <a name="example---document-key-lookup"></a>Exempel – sökning efter dokument nyckel

Endast URL-säkra tecken kan visas i en Azure Search dokument nyckel (eftersom kunderna måste kunna adressera dokumentet med söknings- [API: et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Om käll fältet för nyckeln innehåller URL-osäkra tecken kan du använda `base64Encode` funktionen för att konvertera den vid indexerings tiden.

När du hämtar den kodade nyckeln vid sökning kan du använda `base64Decode` funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Om du inte inkluderar en parameter egenskap för mappnings funktionen används värdet `{"useHttpServerUtilityUrlTokenEncode" : true}`som standard.

Azure Search stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>funktionen base64Decode

Utför base64-avkodning av Indatasträngen. Indatamängden antas vara en *URL-säker* Base64-kodad sträng.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exempel – avkoda BLOB-metadata eller URL: er

Dina källdata kan innehålla base64-kodade strängar, till exempel BLOB-metadata strängar eller webb adresser, som du vill göra sökbara som oformaterad text. Du kan använda `base64Decode` funktionen för att omvandla kodade data tillbaka till vanliga strängar när du fyller i sökindexet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Om du inte tar med en parameter egenskap används värdet `{"useHttpServerUtilityUrlTokenEncode" : true}`som standard.

Azure Search stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-kodnings alternativ

Azure Search stöder två olika base64-kodningar: **HttpServerUtility-URL**-token och **URL-säker base64-kodning utan utfyllnad**. En sträng som är Base64-kodad vid indexering ska senare avkodas med samma kodnings alternativ, eller annars matchar inte resultatet originalet.

`useHttpServerUtilityUrlTokenDecode` `true` `base64Encode` [](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) `base64Decode` Om- eller-parametrarnaförencodingochdeencodingärinställdapå,betersigsedansomHttpServerUtility.UrlTokenEncodeochbetersigsom`useHttpServerUtilityUrlTokenEncode` [ HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Om du inte använder den fullständiga .NET Framework (det vill säga du använder .net Core eller ett annat ramverk) för att skapa de nyckel värden som ska emuleras Azure Search beteende, ska du ställa `useHttpServerUtilityUrlTokenEncode` in `useHttpServerUtilityUrlTokenDecode` och `false`till. Beroende på vilket bibliotek du använder kan base64-kodning och avkodnings funktioner skilja sig från de som används av Azure Search.

I följande tabell jämförs olika base64-kodningar för `00>00?00`strängen. Om du vill fastställa nödvändig ytterligare bearbetning (om det finns någon) för dina base64-funktioner använder du din biblioteks kodnings funktion i strängen `00>00?00` och jämför utdata med förväntade utdata. `MDA-MDA_MDA`

| Kodning | Base64-koda utdata | Ytterligare bearbetning efter biblioteks kodning | Ytterligare bearbetning före biblioteks avkodning |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använd URL-säkra tecken och ta bort utfyllnad | Använd standard base64-tecken och Lägg till utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använd URL-säkra tecken | Använd standard base64-tecken |
| URL – säker base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägg till utfyllnad |
| URL – säker base64 utan utfyllnad | `MDA-MDA_MDA` | Inga | Inga |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>funktionen extractTokenAtPosition

Delar upp ett sträng fält med angiven avgränsare och plockar token på den angivna positionen i den resulterande delningen.

Den här funktionen använder följande parametrar:

* `delimiter`: en sträng som ska användas som avgränsare vid delning av Indatasträngen.
* `position`: en heltal noll-baserad position för den token som ska väljas efter att Indatasträngen har delats upp.

Om till exempel indatatypen är `Jane Doe` `delimiter` , är `" "`(blank steg) `Jane`och `position` är 0, blir `Doe`resultatet. om `position` är 1 är resultatet. Om positionen refererar till en token som inte finns returneras ett fel.

#### <a name="example---extract-a-name"></a>Exempel – extrahera ett namn

Data källan innehåller ett `PersonName` fält och du vill indexera det som två separata `FirstName` fält och `LastName` fält. Du kan använda den här funktionen för att dela upp indatamängden med blank stegs tecken som avgränsare.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>funktionen jsonArrayToStringCollection

Transformerar en sträng formaterad som en JSON-matris med strängar till en sträng mat ris som kan användas för att fylla `Collection(Edm.String)` ett fält i indexet.

Om till exempel `["red", "white", "blue"]`Indatasträngen är, fylls mål fält av typ `Collection(Edm.String)` med de tre värdena `red`, `white`och `blue`. Ett fel returneras för inmatade värden som inte kan parsas som JSON-sträng mat ris.

#### <a name="example---populate-collection-from-relational-data"></a>Exempel – fylla i samling från Relations data

Azure SQL Database har inte någon inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure Search. För att fylla i sträng samlings fält kan du Förbearbeta dina källdata som en JSON-sträng mat ris och sedan `jsonArrayToStringCollection` använda mappnings funktionen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Ett detaljerat exempel som omvandlar Relations data till index samlings fält finns i [modell Relations data](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>funktionen urlEncode

Den här funktionen kan användas för att koda en sträng så att den är "säker URL". När den används med en sträng som innehåller tecken som inte tillåts i en URL, kommer funktionen att konvertera dessa "osäkra" tecken till motsvarande tecken i entiteter. Den här funktionen använder UTF-8-encoding-formatet.

#### <a name="example---document-key-lookup"></a>Exempel – sökning efter dokument nyckel

`urlEncode`funktionen kan användas som ett alternativ till `base64Encode` funktionen, om bara URL-osäkra tecken ska konverteras, samtidigt som de behåller andra tecken.

Anta att Indatasträngen är `<hello>` -och sedan fylls mål fält av typ `(Edm.String)` i med värdet`%3chello%3e`

När du hämtar den kodade nyckeln vid sökning kan du använda `urlDecode` funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>funktionen urlDecode

 Den här funktionen konverterar en URL-kodad sträng till en avkodad sträng med UTF-8-kodat format.

 ### <a name="example---decode-blob-metadata"></a>Exempel – avkoda BLOB-metadata

 Vissa Azure Storage-klienter automatiskt URL-koda BLOB-metadata om det innehåller icke-ASCII-tecken. Men om du vill göra dessa metadata sökbara (som oformaterad text) kan du använda `urlDecode` funktionen för att omvandla kodade data tillbaka till vanliga strängar när du fyller i Sök indexet.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```