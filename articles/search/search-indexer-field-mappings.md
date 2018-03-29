---
title: Fältet avbildningar i Azure Search indexerare
description: Konfigurera Azure Search indexeraren fältet diagrammen efter skillnader mellan fältnamn och representationer av data
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: e4d6960e540641405b879064a8064d45521dc04f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="field-mappings-in-azure-search-indexers"></a>Fältet avbildningar i Azure Search indexerare
När du använder Azure Search indexerare hittar ibland du själv i situationer där dina indata ganska inte matchar schemat för mål-index. I sådana fall kan du använda **fältet mappningar** att omvandla dina data till önskad form.

Vissa situationer där fältmappningar är användbara:

* Datakällan har ett fält `_id`, men Azure Search tillåter inte fältnamn som börjar med ett understreck. En avbildning kan du ”Byt namn på” ett fält.
* Vill du fylla i flera fält i indexet med samma data källdata, till exempel att du vill tillämpa olika analyzers till dessa fält. Fältmappningar kan du ”duplicera” ett fält i datakällan.
* Du måste till Base64 koda eller avkoda dina data. Fältmappningar stöd för flera **mappning funktioner**, inklusive funktioner för Base64-kodning och avkodning.   

## <a name="setting-up-field-mappings"></a>Ställa in fältmappningar
Du kan lägga till fältmappningar när du skapar ett nytt indexerare med det [skapa indexeraren](https://msdn.microsoft.com/library/azure/dn946899.aspx) API. Du kan hantera fältmappningar på en indexering indexerare med det [uppdatering indexeraren](https://msdn.microsoft.com/library/azure/dn946892.aspx) API.

En avbildning består av tre delar:

1. En `sourceFieldName`, som motsvarar ett fält i datakällan. Den här egenskapen måste anges.
2. En valfri `targetFieldName`, som motsvarar ett fält i Sök-index. Om det utelämnas används samma namn som datakällan.
3. En valfri `mappingFunction`, vilket kan omvandla dina data med hjälp av ett av flera fördefinierade funktioner. En fullständig lista över funktioner är [under](#mappingFunctions).

Mappningar av fält har lagts till i den `fieldMappings` matrisen på indexeraren definition.

Här är exempelvis hur du kan hantera skillnader i fältnamn:

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

En indexerare kan ha flera fältmappningar. Till exempel här är hur du kan ”duplicera” ett fält:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search använder skiftlägeskänsliga jämförelser för att matcha namnen på fältet och funktionen i fältet avbildningar. Det här är praktiskt (du behöver få alla gemener och versaler), men det innebär att din datakälla eller index inte kan ha fält som endast skiljer fallet.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fältet mappning funktioner
Dessa funktioner stöds:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Utför *URL-säkert* Base64-kodning av Indatasträngen. Förutsätter att UTF-8-kodade indata.

### <a name="sample-use-case---document-key-lookup"></a>Använd exemplet - dokumentet nyckelsökningen
Endast URL-säkert tecken kan visas i en nyckel för Azure Search-dokument (eftersom kunder måste kunna hantera dokument med den [sökning API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), till exempel). Om dina data innehåller URL-osäkra tecken och du vill använda den för att fylla i nyckelfältet i search-index, använder du den här funktionen. När nyckeln kodas, kan du använda base64 avkoda för att hämta det ursprungliga värdet. Mer information finns i [base64-kodning och avkodning](#base64details) avsnitt.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Använd exemplet - hämta ursprungliga nyckeln
Du har en blob-indexeraren index BLOB-objekt med metadata för blob-sökväg som Dokumentnyckel. När du hämtar dokumentnyckeln kodade, som du vill avkoda sökvägen och hämta blob.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Om du behöver inte slå upp dokument av nycklar och även inte behöver för att avkoda den kodade innehåll, du kan bara lämna `parameters` för funktionen mappning som standard `useHttpServerUtilityUrlTokenEncode` till `true`. Se annars [base64 information](#base64details) avsnittet för att bestämma vilka inställningar som ska användas.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Utför Base64 avkodning av Indatasträngen. Indata antas ett *URL-säkert* Base64-kodad sträng.

### <a name="sample-use-case"></a>Använd exemplet
BLOB anpassade metadatavärden måste vara ASCII-kodade. Du kan använda Base64-kodning för att representera godtycklig UTF-8-strängar i blob anpassade metadata. För att göra sökningen beskrivande använda du dock den här funktionen för att göra kodade data tillbaka till ”vanlig” strängar vid påfyllning search-index.

#### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Om du inte anger någon `parameters`, sedan standardvärdet `useHttpServerUtilityUrlTokenDecode` är `true`. Se [base64 information](#base64details) avsnittet för att bestämma vilka inställningar som ska användas.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Information om base64-kodning och avkodning
Azure Search stöder två base64-kodningar: HttpServerUtility URL: en token och URL: en säker base64-kodning utan utfyllnad. Du måste använda samma kodning som mappning funktioner om du vill koda en Dokumentnyckel för Leta upp, koda ett värde som ska avkodas av indexeraren eller avkoda ett fält som kodats med indexeraren.

Du kan ange om du använder .NET Framework `useHttpServerUtilityUrlTokenEncode` och `useHttpServerUtilityUrlTokenDecode` till `true`, för kodning och avkodning respektive. Sedan `base64Encode` fungerar som [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) och `base64Decode` fungerar som [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Om du inte använder .NET Framework, så du bör ange `useHttpServerUtilityUrlTokenEncode` och `useHttpServerUtilityUrlTokenDecode` till `false`. Beroende på det bibliotek som du använder base64 koda och avkoda verktyget funktioner kan skilja sig från Azure Search.

I följande tabell jämförs olika base64-kodning av strängen `00>00?00`. Använda biblioteket för att fastställa den ytterligare bearbetningen som krävs (eventuella) för base64-funktioner, koda fungerar på strängen `00>00?00` och jämför utdata med utdata som förväntas `MDA-MDA_MDA`.

| Encoding | Base64 koda utdata | Ytterligare bearbetning efter kodning i biblioteket | Ytterligare bearbetning innan avkodning av bibliotek |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använda URL-säkert tecken och ta bort utfyllnad | Använd standard base64-tecken och utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använda URL-säkert tecken | Använd standard base64-tecken |
| URL-säkert base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Utfyllnad |
| URL-säkert base64 utan utfyllnad | `MDA-MDA_MDA` | Ingen | Ingen |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Delar en string-fält med angiven avgränsare och hämtar token vid angiven position i den resulterande delningen.

Om indata är till exempel `Jane Doe`, `delimiter` är `" "`(blanksteg) och `position` är 0, resultatet är `Jane`; om den `position` är 1, resultatet är `Doe`. Om positionen refererar till en token som inte finns, returneras ett fel.

### <a name="sample-use-case"></a>Använd exemplet
Datakällan innehåller en `PersonName` , och du vill indexera som två separata `FirstName` och `LastName` fält. Du kan använda den här funktionen för att dela indata med blanksteg som avgränsare.

### <a name="parameters"></a>Parametrar
* `delimiter`: en sträng som ska användas som avgränsare när du delar upp Indatasträngen.
* `position`: ett heltal nollbaserade plats i token för att välja när Indatasträngen delas.    

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
Omvandlar en sträng formaterad som en JSON-matris med strängar i en strängmatris som kan användas för att fylla i en `Collection(Edm.String)` i indexet.

Om strängen är till exempel `["red", "white", "blue"]`, sedan målfältet av typen `Collection(Edm.String)` fylls med tre värden `red`, `white`, och `blue`. För inkommande värden som inte kan parsas som JSON-strängen matriser, returneras ett fel.

### <a name="sample-use-case"></a>Använd exemplet
Azure SQL-databasen inte har en inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure Search. Om du vill fylla i samlingen strängfält källdata som en strängmatris JSON-format och använda den här funktionen.

### <a name="example"></a>Exempel
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan du nå oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
