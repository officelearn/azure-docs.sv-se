---
title: Fältmappningar för automatisk indexering med hjälp av indexerare – Azure Search
description: Konfigurera Azure Search-indexeraren fältmappningar för skillnader i fältnamn och representationer av data.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9bb8a168f54658ea14b174d19a1795e575646304
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310394"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Fältmappningar i Azure Search-indexerare
När du använder Azure Search-indexerare kan ibland hitta själv i situationer där dina indata ganska inte matchar schemat för ditt målindex. I sådana fall kan du använda **fältmappningar** att omvandla data till önskad form.

Vissa situationer där fältmappningar är användbara:

* Datakällan har ett fält `_id`, men Azure Search tillåter inte fältnamn som börjar med ett understreck. En fältmappning kan du ”byta namn” ett fält.
* Vill du fylla i flera fält i indexet med samma data källdata, till exempel att du vill tillämpa olika analysatorer till dessa fält. Fältmappningar kan du ”Förgrena” ett fält i datakällan.
* Du behöver till Base64 koda eller avkoda dina data. Fältmappningar stöd för flera **mappning funktioner**, inklusive funktioner för Base64-kodning och avkodning.   

## <a name="setting-up-field-mappings"></a>Konfigurera fältmappningar
Du kan lägga till fältmappningar när du skapar en ny indexerare med den [skapa et indexerare](https://msdn.microsoft.com/library/azure/dn946899.aspx) API. Du kan hantera fältmappningar på ett indexering indexerare med hjälp av den [uppdatering indexeraren](https://msdn.microsoft.com/library/azure/dn946892.aspx) API.

En fältmappning består av tre delar:

1. En `sourceFieldName`, som representerar ett fält i datakällan. Den här egenskapen måste anges.
2. En valfri `targetFieldName`, som representerar ett fält i sökindexet. Om det utelämnas används samma namn som i datakällan.
3. En valfri `mappingFunction`, vilket kan omvandla dina data med ett av flera fördefinierade funktioner. En fullständig lista över funktioner är [nedan](#mappingFunctions).

Fält-mappningar har lagts till i `fieldMappings` matrisen på att indexerarens definition.

Till exempel är här hur du kan hantera skillnader i fältnamn:

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

En indexerare kan ha flera fältmappningar. Till exempel, här är hur du kan ”Förgrena” ett fält:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Mappning av funktioner
Dessa funktioner stöds för närvarande:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Utför *URL-safe* Base64-kodning av Indatasträngen. Förutsätter att indata är UTF-8-kodat.

### <a name="sample-use-case---document-key-lookup"></a>Använd exemplet - dokumentet nyckelsökningen
Endast URL-safe-tecken kan visas i en Azure Search-dokumentnyckeln (eftersom kunder måste kunna lösa dokumentet med den [Lookup API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), till exempel). Om dina data innehåller URL-osäkra tecknen och du vill använda den för att fylla i ett nyckelfält i sökindexet, Använd den här funktionen. När nyckeln är kodat, kan du använda base64 avkoda för att hämta det ursprungliga värdet. Mer information finns i [base64-kodning och avkodning](#base64details) avsnittet.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Exempel på användningsfall – hämta ursprungliga nyckeln
Du har en blob-indexeraren index blobbar med sökvägen blobbmetadata som dokumentnyckeln. När du har hämtat den kodade dokumentnyckeln som du vill avkoda sökvägen och ladda ned bloben.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Om du behöver inte leta upp dokument med nycklar och också behöver för att avkoda den kodade innehåll, du kan bara lämna inte `parameters` för funktionen mappning som standard `useHttpServerUtilityUrlTokenEncode` till `true`. Annars läser [base64 information](#base64details) avsnitt för att bestämma vilka inställningar som ska användas.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Utför Base64 avkodning av Indatasträngen. Indata antas ett *URL-safe* Base64-kodad sträng.

### <a name="sample-use-case"></a>Exemplet användningsfall
BLOB anpassade metadatavärden måste vara ASCII-kodade. Du kan använda Base64-kodning för att representera godtyckliga UTF-8-strängar i anpassade blobmetadata. Men för att göra sökningen meningsfulla, kan du använda den här funktionen för att förvandla kodade data tillbaka till ”vanliga” strängar när du fyller search-index.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Om du inte anger någon `parameters`, sedan standardvärdet `useHttpServerUtilityUrlTokenDecode` är `true`. Se [base64 information](#base64details) avsnitt för att bestämma vilka inställningar som ska användas.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Information om base64-kodning och avkodning
Azure Search har stöd för två base64-kodning: HttpServerUtility URL: en token och URL-safe base64-kodning utan utfyllnad. Du måste använda samma kodning som mappning funktioner om du vill koda en dokumentnyckeln för titt upp, koda ett värde till att avkoda av indexeraren eller avkoda ett fält som kodats med indexeraren.

Om `useHttpServerUtilityUrlTokenEncode` eller `useHttpServerUtilityUrlTokenDecode` parametrar för att koda och avkoda respektive ställs in till `true`, sedan `base64Encode` fungerar som [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) och `base64Decode` fungerar som [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Om du inte använder fullständiga .NET Framework (d.v.s. du använder .NET Core eller andra programmeringsmiljö) för att generera nyckelvärden för att emulera beteendet för Azure Search, du bör konfigurera `useHttpServerUtilityUrlTokenEncode` och `useHttpServerUtilityUrlTokenDecode` till `false`. Beroende på i biblioteket som du använder base64 koda och avkoda verktyget funktioner kan skilja sig från Azure Search.

I följande tabell jämförs olika base64-kodning av strängen `00>00?00`. Använda biblioteket för att fastställa den ytterligare bearbetningen som krävs (i förekommande fall) för dina base64-funktioner, kodas funktionen på strängen `00>00?00` och jämför resultatet med utdata som förväntas `MDA-MDA_MDA`.

| Kodning | Base64-koda utdata | Ytterligare bearbetning efter biblioteket kodning | Ytterligare bearbetning innan du biblioteket avkodning |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använd URL-safe-tecken och ta bort utfyllnad | Använd standard base64-tecken och Lägg till utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använd URL-safe-tecken | Använd standard base64-tecken |
| URL-safe base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägg till utfyllnad |
| URL-safe base64 utan utfyllnad | `MDA-MDA_MDA` | Ingen | Ingen |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Delar upp ett strängfält med angiven avgränsare och hämtar en token vid den angivna positionen i den resulterande delningen.

Exempel: om indata är `Jane Doe`, `delimiter` är `" "`(blanksteg) och `position` är 0, resultatet är `Jane`; om den `position` är 1, resultatet är `Doe`. Om positionen refererar till en token som inte finns, returneras ett fel.

### <a name="sample-use-case"></a>Exemplet användningsfall
Datakällan innehåller en `PersonName` , och du vill indexera det som två separata `FirstName` och `LastName` fält. Du kan använda den här funktionen för att dela upp indata med blanksteg som avgränsare.

### <a name="parameters"></a>Parametrar
* `delimiter`: en sträng som ska användas som avgränsare när du delar upp strängen.
* `position`: ett heltal nollbaserade plats av token att välja när den inmatade strängen delas upp.    

### <a name="example"></a>Exempel
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Omvandlar en sträng som formaterats som en JSON-matris med strängar som en strängmatris som kan användas för att fylla i en `Collection(Edm.String)` i indexet.

Exempel: om den inmatade strängen är `["red", "white", "blue"]`, sedan målfältet av typen `Collection(Edm.String)` fylls i med de tre värdena `red`, `white`, och `blue`. Ange olika värden som inte kan parsas som JSON-sträng matriser, returneras ett fel.

### <a name="sample-use-case"></a>Exemplet användningsfall
Azure SQL-databas som inte har en inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure Search. För att fylla i samlingen strängfält, formatera dina källdata som strängmatris JSON och Använd den här funktionen.

### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar eller idéer om förbättringar, kontaktar du oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
