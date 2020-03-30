---
title: Fältmappningar i indexerare
titleSuffix: Azure Cognitive Search
description: Konfigurera fältmappningar i en indexerare för att ta hänsyn till skillnader i fältnamn och datarepresentationer.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275160"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Fältmappningar och omvandlingar med Hjälp av Azure Cognitive Search-indexerare

När du använder Azure Cognitive Search-indexerare upptäcker du ibland att indata inte riktigt matchar schemat för ditt målindex. I sådana fall kan du använda **fältmappningar** för att omforma dina data under indexeringsprocessen.

Vissa situationer där fältmappningar är användbara:

* Datakällan har ett `_id`fält med namnet , men Azure Cognitive Search tillåter inte fältnamn som börjar med ett understreck. Med en fältmappning kan du byta namn på ett fält på ett effektivt sätt.
* Du vill fylla i flera fält i indexet från samma datakälldata. Du kanske till exempel vill använda olika analysatorer på dessa fält.
* Du vill fylla i ett indexfält med data från mer än en datakälla, och datakällorna använder olika fältnamn.
* Du måste base64 koda eller avkoda dina data. Fältmappningar stöder flera **mappningsfunktioner,** inklusive funktioner för Base64-kodning och avkodning.

> [!NOTE]
> Fältmappningsfunktionen i Azure Cognitive Search-indexerare är ett enkelt sätt att mappa datafält till indexfält, med några alternativ för datakonvertering. Mer komplexa data kan kräva förbearbetning för att omforma dem till ett formulär som är enkelt att indexera.
>
> Microsoft Azure Data Factory är en kraftfull molnbaserad lösning för import och omvandling av data. Du kan också skriva kod för att omvandla källdata innan du indexerar. Kodexempel finns i [Modellrelationsdata](search-example-adventureworks-modeling.md) och [modellfaceter på flera nivåer](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Ställa in fältmappningar

En fältkartläggning består av tre delar:

1. A `sourceFieldName`, som representerar ett fält i datakällan. Boendet är obligatoriskt.
2. Ett `targetFieldName`valfritt , som representerar ett fält i sökindexet. Om det utelämnas används samma namn som i datakällan.
3. En `mappingFunction`valfri , som kan omvandla dina data med hjälp av en av flera fördefinierade funktioner. Den fullständiga listan över funktioner finns [nedan](#mappingFunctions).

Fältmappningar läggs till `fieldMappings` i matrisen för indexeringsdefinitionen.

## <a name="map-fields-using-the-rest-api"></a>Mappa fält med REST API

Du kan lägga till fältmappningar när du skapar en ny indexerare med hjälp av [API-begäran skapa indexerare.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Du kan hantera fältmappningar för en befintlig indexerare med hjälp av [API-begäran för Uppdatera indexerare.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Så här mappar du till exempel ett källfält till ett målfält med ett annat namn:

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

Ett källfält kan refereras i flera fältmappningar. I följande exempel visas hur du "gaffel" ett fält, kopiera samma källfält till två olika indexfält:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search använder skiftlägesokänslig jämförelse för att matcha fält- och funktionsnamnen i fältmappningar. Detta är praktiskt (du behöver inte få alla höljet rätt), men det innebär att din datakälla eller index inte kan ha fält som skiljer sig endast från fall.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mappa fält med .NET SDK

Du definierar fältmappningar i .NET SDK med klassen [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) som har egenskaperna `SourceFieldName` och `TargetFieldName`och en valfri `MappingFunction` referens.

Du kan ange fältmappningar när du skapar indexeraren `Indexer.FieldMappings` eller senare genom att direkt ange egenskapen.

I följande C#-exempel anges fältmappningarna när du skapar en indexerare.

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

## <a name="field-mapping-functions"></a>Funktioner för fältmappning

En fältmappningsfunktion omvandlar innehållet i ett fält innan det lagras i indexet. Följande mappningsfunktioner stöds för närvarande:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extraktTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringKollera](#jsonArrayToStringCollectionFunction)
* [urlEncode (urlEncode)](#urlEncodeFunction)
* [urlDecode (urlDecode)](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>funktionen base64Encode

Utför *URL-säker* Base64-kodning av indatasträngen. Förutsätter att indata är UTF-8-kodad.

#### <a name="example---document-key-lookup"></a>Exempel - uppslag på dokumentnyckel

Endast URL-säkra tecken kan visas i en Azure Cognitive Search-dokumentnyckel (eftersom kunderna måste kunna adressera dokumentet med hjälp av [uppslags-API).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Om källfältet för nyckeln innehåller URL-osäkra tecken kan `base64Encode` du använda funktionen för att konvertera det vid indexeringstiden. En dokumentnyckel (både före och efter konvertering) kan dock inte vara längre än 1 024 tecken.

När du hämtar den kodade nyckeln vid sökningen `base64Decode` kan du sedan använda funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta källdokumentet.

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

Om du inte inkluderar en parameteregenskap för mappningsfunktionen `{"useHttpServerUtilityUrlTokenEncode" : true}`är värdet som standard .

Azure Cognitive Search stöder två olika Base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64-kodningsalternativ](#base64details) för att bestämma vilka parametrar som ska användas.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>funktionen base64Decode

Utför Base64 avkodning av indatasträngen. Indata antas vara en *URL-säker* Base64-kodad sträng.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exempel - avkoda blobmetadata eller webbadresser

Källdata kan innehålla Base64-kodade strängar, till exempel blobmetadatasträngar eller webb-URL:er, som du vill göra sökbara som oformaterad text. Du kan `base64Decode` använda funktionen för att återföra kodade data till vanliga strängar när du fyller i sökindexet.

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

Om du inte inkluderar en parameteregenskap är `{"useHttpServerUtilityUrlTokenEncode" : true}`värdet som standard standard .

Azure Cognitive Search stöder två olika Base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64-kodningsalternativ](#base64details) för att bestämma vilka parametrar som ska användas.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>alternativ för bas64-kodning

Azure Cognitive Search stöder URL-säker base64-kodning och normal base64-kodning. En sträng som är base64 kodad under indexering ska avkodas senare med samma kodningsalternativ, annars matchar resultatet inte originalet.

Om `useHttpServerUtilityUrlTokenEncode` parametrarna `useHttpServerUtilityUrlTokenDecode` eller för kodning respektive avkodning `true`är `base64Encode` inställda på , beter sig sedan som `base64Decode` [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) och fungerar som [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Om `base64Encode` används för att `useHttpServerUtilityUrlTokenEncode` skapa nyckelvärden måste värdet vara true. Endast URL-säker base64-kodning kan användas för nyckelvärden. Se [Namngivningsregler &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) för fullständig uppsättning begränsningar för tecken i nyckelvärden.

.NET-biblioteken i Azure Cognitive Search antar hela .NET Framework, som tillhandahåller inbyggd kodning. Alternativen `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` och utnyttjar den här inbyggda funktionsfunktionen. Om du använder .NET Core eller ett annat `false` ramverk rekommenderar vi att du ställer in dessa alternativ till och anropar ramverkets kodnings- och avkodningsfunktioner direkt.

I följande tabell jämförs olika base64-kodningar `00>00?00`för strängen . Om du vill ta reda på vilken ytterligare bearbetning (om sådan finns) `00>00?00` för dina base64-funktioner använder du bibliotekskodfunktionen på strängen och jämför utdata med den förväntade utdata `MDA-MDA_MDA`.

| Kodning | Base64 koda utdata | Ytterligare bearbetning efter bibliotekskodning | Ytterligare bearbetning före avkodning av bibliotek |
| --- | --- | --- | --- |
| Base64 med stoppning | `MDA+MDA/MDA=` | Använda URL-säkra tecken och ta bort utfyllnad | Använda standardbas64-tecken och lägg till utfyllnad |
| Base64 utan stoppning | `MDA+MDA/MDA` | Använda URL-säkra tecken | Använda standardbas64-tecken |
| URL-säker bas64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägga till utfyllnad |
| URL-säker bas64 utan utfyllnad | `MDA-MDA_MDA` | Inget | Inget |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>funktionen extractTokenAtPosition

Delar ett strängfält med den angivna avgränsaren och väljer token på angiven position i den resulterande delningen.

Den här funktionen använder följande parametrar:

* `delimiter`: en sträng som ska användas som avgränsare när indatasträngen delas upp.
* `position`: en heltals-nollbaserad position för token som ska plockas efter att indatasträngen har delats.

Om indata till `Jane Doe`exempel `delimiter` är `" "`, är `position` (mellanslag) och `Jane`är 0, blir resultatet ; om `position` är 1, blir `Doe`resultatet . Om befattningen refererar till en token som inte finns returneras ett fel.

#### <a name="example---extract-a-name"></a>Exempel - extrahera ett namn

Datakällan innehåller `PersonName` ett fält och du vill indexera det som två separata `FirstName` fält och `LastName` fält. Du kan använda den här funktionen för att dela indata med blankstegstecknet som avgränsare.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringKollektionsfunktion

Omvandlar en sträng formaterad som en JSON-matris med strängar till en `Collection(Edm.String)` strängmatris som kan användas för att fylla i ett fält i indexet.

Om indatasträngen till `["red", "white", "blue"]`exempel är fylls `Collection(Edm.String)` målfältet av typen `red`med `white`de `blue`tre värdena , och . För indatavärden som inte kan tolkas som JSON-strängmatriser returneras ett fel.

#### <a name="example---populate-collection-from-relational-data"></a>Exempel - fylla i insamling från relationsdata

Azure SQL Database har inte en inbyggd datatyp `Collection(Edm.String)` som naturligt mappar till fält i Azure Cognitive Search. Om du vill fylla i strängsamlingsfält kan du förbehandla källdata `jsonArrayToStringCollection` som en JSON-strängmatris och sedan använda mappningsfunktionen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Ett detaljerat exempel som omvandlar relationsdata till indexinsamlingsfält finns i [Modellrelationsdata](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode-funktion

Denna funktion kan användas för att koda en sträng så att den är "URL säker". När den används med en sträng som innehåller tecken som inte är tillåtna i en URL konverteras dessa "osäkra" tecken till teckenettitetsekvivalenter. Den här funktionen använder UTF-8-kodningsformatet.

#### <a name="example---document-key-lookup"></a>Exempel - uppslag på dokumentnyckel

`urlEncode`funktionen kan användas som ett `base64Encode` alternativ till funktionen, om bara URL osäkra tecken ska konverteras, samtidigt som andra tecken som är.

Säg, indatasträngen är `<hello>` - då `(Edm.String)` kommer målfältet av typen att fyllas med värdet`%3chello%3e`

När du hämtar den kodade nyckeln vid sökningen `urlDecode` kan du sedan använda funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta källdokumentet.

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

 ### <a name="urldecode-function"></a>urlDecode-funktion

 Den här funktionen konverterar en URL-kodad sträng till en avkodad sträng med UTF-8-kodningsformat.

 ### <a name="example---decode-blob-metadata"></a>Exempel - avkoda blobmetadata

 Vissa Azure-lagringsklienter url kodar automatiskt blobmetadata om de innehåller icke-ASCII-tecken. Men om du vill göra sådana metadata sökbara (som `urlDecode` oformaterad text) kan du använda funktionen för att återföra kodade data till vanliga strängar när du fyller i sökindexet.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode-funktionen
 
 Den här funktionen konverterar en sträng av valfri längd till en fast längdsträng.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exempel - kartdokumentnycklar som är för långa
 
När du står inför fel klagar dokumentnyckel är längre än 1024 tecken, kan denna funktion användas för att minska längden på dokumentnyckeln.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
