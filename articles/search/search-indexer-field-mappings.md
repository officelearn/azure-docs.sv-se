---
title: Fältmappningar i indexerare
titleSuffix: Azure Cognitive Search
description: Konfigurera fält mappningar i en indexerare för att redovisa skillnader i fält namn och data representationer.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72623787cdb27c568fe2b4ec075010674a3996ef
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123988"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Fält mappningar och transformeringar med Azure Kognitiv sökning indexerare

När du använder Azure Kognitiv sökning indexerare ser du ibland till att indata inte stämmer överens med schemat för ditt mål index. I dessa fall kan du använda **fält mappningar** för att ändra form på dina data under indexerings processen.

Några situationer där fält mappningar är användbara:

* Din data källa har ett fält med namnet `_id`, men Azure Kognitiv sökning tillåter inte fält namn som börjar med ett under streck. Med en fält mappning kan du effektivt byta namn på ett fält.
* Du vill fylla i flera fält i indexet från samma data käll data. Du kanske till exempel vill använda olika analys verktyg för dessa fält.
* Du vill fylla i ett index fält med data från fler än en data källa och data källorna använder olika fält namn.
* Du måste base64 koda eller avkoda dina data. Fält mappningar har stöd för flera **mappnings funktioner**, inklusive funktioner för base64-kodning och avkodning.

> [!NOTE]
> Fält mappnings funktionen i Azure Kognitiv sökning indexerare ger ett enkelt sätt att mappa data fält till index fält, med några alternativ för data konvertering. Mer komplexa data kan kräva för bearbetning för att forma om till ett formulär som är enkelt att indexera.
>
> Microsoft Azure Data Factory är en kraftfull molnbaserad lösning för import och omvandling av data. Du kan också skriva kod för att transformera källdata före indexering. Kod exempel finns i [modell Relations data](search-example-adventureworks-modeling.md) och [modeller på flera nivåer](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Konfigurera fält mappningar

En fält mappning består av tre delar:

1. En `sourceFieldName`som representerar ett fält i data källan. Den här egenskapen är obligatorisk.
2. En valfri `targetFieldName`som representerar ett fält i Sök indexet. Om det utelämnas används samma namn som i data källan.
3. En valfri `mappingFunction`som kan omvandla dina data med hjälp av en av flera fördefinierade funktioner. Den fullständiga listan över funktioner finns [nedan](#mappingFunctions).

Fält mappningar läggs till i den `fieldMappings` matrisen för index definitions definitionen.

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
> Azure Kognitiv sökning använder SKIFT läges okänslig jämförelse för att matcha fält-och funktions namn i fält mappningar. Detta är praktiskt (du behöver inte hämta alla Skift läges rättigheter), men det innebär att data källan eller indexet inte kan ha fält som bara skiljer sig från versaler.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mappa fält med hjälp av .NET SDK

Du definierar fält mappningar i .NET SDK med [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) -klassen, som har egenskaperna `SourceFieldName` och `TargetFieldName`, och en valfri `MappingFunction` referens.

Du kan ange fält mappningar när du konstruerar indexeraren eller senare genom att ange egenskapen `Indexer.FieldMappings` direkt.

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

Endast URL-säkra tecken kan visas i en Azure Kognitiv sökning-dokument nyckel (eftersom kunderna måste kunna adressera dokumentet med [söknings-API: et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Om käll fältet för nyckeln innehåller URL-osäkra tecken kan du använda funktionen `base64Encode` för att konvertera den vid indexerings tiden.

När du hämtar den kodade nyckeln vid sökning kan du använda funktionen `base64Decode` för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

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

Azure Kognitiv sökning stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>funktionen base64Decode

Utför base64-avkodning av Indatasträngen. Indatamängden antas vara en *URL-säker* Base64-kodad sträng.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exempel – avkoda BLOB-metadata eller URL: er

Dina källdata kan innehålla base64-kodade strängar, till exempel BLOB-metadata strängar eller webb adresser, som du vill göra sökbara som oformaterad text. Du kan använda funktionen `base64Decode` för att omvandla kodade data tillbaka till vanliga strängar när du fyller i sökindexet.

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

Azure Kognitiv sökning stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-kodnings alternativ

Azure Kognitiv sökning stöder URL-säker base64-kodning och normal base64-kodning. En sträng som är Base64-kodad vid indexering ska avkodas senare med samma kodnings alternativ, eller annars matchar inte resultatet originalet.

Om `useHttpServerUtilityUrlTokenEncode`-eller `useHttpServerUtilityUrlTokenDecode`-parametrarna för kodning och avkodning anges till `true`kommer `base64Encode` beter sig som [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) och `base64Decode` fungerar som [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Om `base64Encode` används för att skapa nyckel värden måste `useHttpServerUtilityUrlTokenEncode` anges till sant. Endast URL-säker base64-kodning kan användas för nyckel värden. Se [namngivnings &#40;regler Azure&#41; kognitiv sökning](https://docs.microsoft.com/rest/api/searchservice/naming-rules) för att få en fullständig uppsättning begränsningar för tecken i nyckel värden.

.NET-biblioteken i Azure Kognitiv sökning antar den fullständiga .NET Framework, som innehåller inbyggd kodning. Alternativen `useHttpServerUtilityUrlTokenEncode` och `useHttpServerUtilityUrlTokenDecode` utnyttjar den här inbyggda functionaity. Om du använder .NET Core eller ett annat ramverk rekommenderar vi att du ställer in de här alternativen för att `false` och anropa ramverkets kodning och avkodnings funktioner direkt.

I följande tabell jämförs olika base64-kodningar för sträng `00>00?00`. Om du vill ta reda på vilken ytterligare bearbetning som krävs för dina base64-funktioner, använder du din biblioteks kodnings funktion i strängen `00>00?00` och jämför utdata med förväntad utdata `MDA-MDA_MDA`.

| Kodning | Base64-koda utdata | Ytterligare bearbetning efter biblioteks kodning | Ytterligare bearbetning före biblioteks avkodning |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använd URL-säkra tecken och ta bort utfyllnad | Använd standard base64-tecken och Lägg till utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använd URL-säkra tecken | Använd standard base64-tecken |
| URL – säker base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägg till utfyllnad |
| URL – säker base64 utan utfyllnad | `MDA-MDA_MDA` | Ingen | Ingen |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>funktionen extractTokenAtPosition

Delar upp ett sträng fält med angiven avgränsare och plockar token på den angivna positionen i den resulterande delningen.

Den här funktionen använder följande parametrar:

* `delimiter`: en sträng som ska användas som avgränsare vid delning av Indatasträngen.
* `position`: en heltals noll-baserad position för den token som ska väljas efter att Indatasträngen är delad.

Om indatatypen till exempel är `Jane Doe`är `delimiter` `" "`(blank steg) och `position` är 0 `Jane`resultatet. om `position` är 1 är resultatet `Doe`. Om positionen refererar till en token som inte finns returneras ett fel.

#### <a name="example---extract-a-name"></a>Exempel – extrahera ett namn

Din data källa innehåller ett `PersonName` fält och du vill indexera det som två separata `FirstName`-och `LastName` fält. Du kan använda den här funktionen för att dela upp indatamängden med blank stegs tecken som avgränsare.

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

Omvandlar en sträng formaterad som en JSON-matris med strängar till en sträng mat ris som kan användas för att fylla i ett `Collection(Edm.String)` fält i indexet.

Om Indatasträngen till exempel är `["red", "white", "blue"]`, fylls mål fältet av typen `Collection(Edm.String)` med de tre värdena `red`, `white`och `blue`. Ett fel returneras för inmatade värden som inte kan parsas som JSON-sträng mat ris.

#### <a name="example---populate-collection-from-relational-data"></a>Exempel – fylla i samling från Relations data

Azure SQL Database har inte någon inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure Kognitiv sökning. För att fylla i sträng samlings fält kan du Förbearbeta dina källdata som en JSON-sträng mat ris och sedan använda mappnings funktionen för `jsonArrayToStringCollection`.

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

`urlEncode` funktionen kan användas som ett alternativ till funktionen `base64Encode`, om bara URL-osäkra tecken ska konverteras, samtidigt som de behåller andra tecken.

Anta att Indatasträngen är `<hello>`-sedan fylls mål fältet av typen `(Edm.String)` med värdet `%3chello%3e`

När du hämtar den kodade nyckeln vid sökning kan du använda funktionen `urlDecode` för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

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

 Vissa Azure Storage-klienter automatiskt URL-koda BLOB-metadata om det innehåller icke-ASCII-tecken. Men om du vill göra dessa metadata sökbara (som oformaterad text) kan du använda funktionen `urlDecode` för att omvandla kodade data tillbaka till vanliga strängar när du fyller i Sök indexet.

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