---
title: Fältmappningar för automatisk indexering med hjälp av indexerare – Azure Search
description: Konfigurera Azure Search-indexeraren fältmappningar för skillnader i fältnamn och representationer av data.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147793"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Fältmappningar och transformeringar som använder Azure Search-indexerare

När du använder Azure Search-indexerare kan hitta du ibland att indata ganska inte matchar schemat för ditt målindex. I sådana fall kan du använda **fältmappningar** kan formatera om dina data under indexering.

Vissa situationer där fältmappningar är användbara:

* Datakällan har ett fält med namnet `_id`, men Azure Search tillåter inte fältnamn som börjar med ett understreck. En fältmappning kan du effektivt Byt namn på ett fält.
* Vill du fylla i flera fält i indexet från samma data källdata. Du kanske exempelvis vill olika analysverktyg som tillämpas på dessa fält.
* Du vill fylla i ett indexfält med data från mer än en datakälla och datakällorna varje använder olika fältnamn.
* Du behöver till Base64 koda eller avkoda dina data. Fältmappningar stöd för flera **mappning funktioner**, inklusive funktioner för Base64-kodning och avkodning.

> [!NOTE]
> Funktionen för mappning av fält i Azure Search-indexerare tillhandahåller ett enkelt sätt att mappa fält till index-fält, med ett par alternativ för datakonvertering. Mer komplexa data kan kräva att bearbeta data i förväg om du vill ändra det till ett format som är lätt att index.
>
> Microsoft Azure Data Factory är en kraftfull molnbaserad lösning för import och transformera data. Du kan också skriva kod för att transformera källdata innan indexering. Kodexempel Se [modeller med relationsdata](search-example-adventureworks-modeling.md) och [modellera flera nivåer fasetter](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Konfigurera fältmappningar

En fältmappning består av tre delar:

1. En `sourceFieldName`, som representerar ett fält i datakällan. Den här egenskapen måste anges.
2. En valfri `targetFieldName`, som representerar ett fält i sökindexet. Om det utelämnas används samma namn som i datakällan.
3. En valfri `mappingFunction`, vilket kan omvandla dina data med ett av flera fördefinierade funktioner. En fullständig lista över funktioner är [nedan](#mappingFunctions).

Fältmappningar läggs till i `fieldMappings` matris med att indexerarens definition.

## <a name="map-fields-using-the-rest-api"></a>Mappa fält med hjälp av REST-API

Du kan lägga till fältmappningar när du skapar en ny indexerare med den [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API-begäran. Du kan hantera fältmappningar för en befintlig indexerare med den [uppdatering indexeraren](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API-begäran.

Här är till exempel hur du mappar ett fält i datakällan till ett målfält med ett annat namn:

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

Ett fält i datakällan kan refereras i flera fält-mappningar. I följande exempel visar hur du ”Förgrena” ett fält, kopiera samma fält i datakällan till två olika index-fält:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search använder skiftlägesoberoende jämförelse för att matcha namnen på fältet och funktion i fält-mappningar. Detta är praktiskt (du behöver få alla gemener och versaler), men det innebär att din datakälla eller index inte kan ha fält som endast skiljer fallet.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mappa fält med hjälp av .NET SDK

Du definierar fältmappningar i .NET SDK med den [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) klass som har egenskaper `SourceFieldName` och `TargetFieldName`, och en valfri `MappingFunction` referens.

Du kan ange fältmappningar när indexeraren eller senare genom att ange direkt i `Indexer.FieldMappings` egenskapen.

Följande C# exempel anger fältmappningarna när en indexerare.

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

## <a name="field-mapping-functions"></a>Mappning av funktioner

En funktion för mappning av fältet transformerar innehållet i ett fält innan de lagras i indexet. Följande mappning funktioner stöds för närvarande:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode funktion

Utför *URL-safe* Base64-kodning av Indatasträngen. Förutsätter att indata är UTF-8-kodat.

#### <a name="example---document-key-lookup"></a>Exempel – dokumentet nyckelsökningen

Endast URL-safe-tecken kan visas i en Azure Search-dokumentnyckeln (eftersom kunder måste kunna lösa dokumentet med den [Lookup API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Om fältet källa för din nyckel innehåller URL-osäkra tecknen, kan du använda den `base64Encode` att konvertera vid indexering tid.

När du hämtar nyckeln kodade på söktiden, du kan sedan använda den `base64Decode` för att hämta värdet för ursprungliga nyckeln och använda den för att hämta källdokument.

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

Om du inte anger en egenskap för parametrar för funktionen mappning, används värdet `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Azure Search har stöd för två olika Base64-kodningar. Du bör använda samma parametrar när kodning och avkodning samma fält. Mer information finns i [base64 kodningsalternativ](#base64details) att bestämma vilka parametrar som ska användas.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode funktion

Utför Base64 avkodning av Indatasträngen. Indata antas vara en *URL-safe* Base64-kodad sträng.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exempel – avkoda blob-metadata eller URL: er

Dina källdata kan innehålla Base64-kodade strängar, till exempel blob metadata strängar eller webbadresser som du vill ska vara sökbart som oformaterad text. Du kan använda den `base64Decode` att förvandla kodade data tillbaka till vanlig strängar när du fyller search-index.

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

Om du inte anger en egenskap för parametrar, används värdet `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Azure Search har stöd för två olika Base64-kodningar. Du bör använda samma parametrar när kodning och avkodning samma fält. Mer information finns i [base64 kodningsalternativ](#base64details) att bestämma vilka parametrar som ska användas.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-kodning alternativ

Azure Search har stöd för två olika Base64-kodning: **HttpServerUtility URL: en token**, och **URL-safe Base64-kodning utan utfyllnad**. En sträng som är base64-kodad under indexering senare att avkoda med samma kodningsalternativ, eller så resultatet matchar inte ursprungligt.

Om den `useHttpServerUtilityUrlTokenEncode` eller `useHttpServerUtilityUrlTokenDecode` parametrar för att koda och avkoda respektive ställs in till `true`, sedan `base64Encode` fungerar som [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) och `base64Decode` fungerar som [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Om du inte använder fullständiga .NET Framework (det vill säga du använder .NET Core eller en annan framework) för att generera nyckelvärden för att emulera beteendet för Azure Search, du bör konfigurera `useHttpServerUtilityUrlTokenEncode` och `useHttpServerUtilityUrlTokenDecode` till `false`. I biblioteket som du använder base64-kodning och avkodning funktioner skiljer sig från de som används av Azure Search.

I följande tabell jämförs olika base64-kodning av strängen `00>00?00`. Använda biblioteket för att fastställa den ytterligare bearbetningen som krävs (i förekommande fall) för dina base64-funktioner, kodas funktionen på strängen `00>00?00` och jämför resultatet med utdata som förväntas `MDA-MDA_MDA`.

| Kodning | Base64-koda utdata | Ytterligare bearbetning efter biblioteket kodning | Ytterligare bearbetning innan du biblioteket avkodning |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använd URL-safe-tecken och ta bort utfyllnad | Använd standard base64-tecken och Lägg till utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använd URL-safe-tecken | Använd standard base64-tecken |
| URL-safe base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägg till utfyllnad |
| URL-safe base64 utan utfyllnad | `MDA-MDA_MDA` | Ingen | Ingen |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition funktion

Delar upp ett strängfält med angiven avgränsare och hämtar en token vid den angivna positionen i den resulterande delningen.

Den här funktionen använder följande parametrar:

* `delimiter`: en sträng som ska användas som avgränsare när du delar upp strängen.
* `position`: ett heltal nollbaserade plats av token att välja när den inmatade strängen delas upp.

Exempel: om indata är `Jane Doe`, `delimiter` är `" "`(blanksteg) och `position` är 0, resultatet är `Jane`; om den `position` är 1, resultatet är `Doe`. Om positionen refererar till en token som inte finns, returneras ett fel.

#### <a name="example---extract-a-name"></a>Exempel – extrahera ett namn

Datakällan innehåller en `PersonName` , och du vill indexera det som två separata `FirstName` och `LastName` fält. Du kan använda den här funktionen för att dela upp indata med blanksteg som avgränsare.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection funktion

Omvandlar en sträng som formaterats som en JSON-matris med strängar som en strängmatris som kan användas för att fylla i en `Collection(Edm.String)` i indexet.

Exempel: om den inmatade strängen är `["red", "white", "blue"]`, sedan målfältet av typen `Collection(Edm.String)` fylls i med de tre värdena `red`, `white`, och `blue`. Ange olika värden som inte kan parsas som JSON-sträng matriser, returneras ett fel.

#### <a name="example---populate-collection-from-relational-data"></a>Exempel – Lägg till i samlingen av relationsdata

Azure SQL-databas som inte har en inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure Search. För att fylla i samlingen strängfält, kan du Förbearbeta dina källdata som strängmatris JSON och sedan använda den `jsonArrayToStringCollection` mappning av funktionen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Ett detaljerat exempel som omvandlar relationella data till indexet samling fält, se [modeller med relationsdata](search-example-adventureworks-modeling.md).
