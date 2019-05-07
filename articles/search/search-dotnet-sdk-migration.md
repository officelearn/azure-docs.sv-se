---
title: Uppgradera till Azure Search .NET SDK version 3 – Azure Search
description: Migrera kod till Azure Search .NET SDK version 3 från äldre versioner. Läs mer om nyheterna och vilka kodändringar krävs.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d41c2b541bf80448d180a1d081c255e5bf754e5e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147338"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Uppgradera till Azure Search .NET SDK version 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Om du använder version 2.0 preview eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera programmet att använda version 3.

En mer allmän genomgång av SDK inklusive exempel finns i [hur du använder Azure Search från .NET-program](search-howto-dotnet-sdk.md).

Version 3 av Azure Search .NET SDK innehåller vissa ändringar från tidigare versioner. Det här är främst mindre, så ändra din kod kräver bara minimal ansträngning. Se [stegen för att uppgradera](#UpgradeSteps) anvisningar om hur du ändrar din kod till den nya versionen av SDK.

> [!NOTE]
> Om du använder version 1.0.2-preview eller äldre, bör du uppgradera till version 1.1 först och därefter uppgradera till version 3. Se [uppgradering till Azure Search .NET SDK version 1.1](search-dotnet-sdk-migration-version-1.md) anvisningar.
>
> Din Azure Search-tjänstinstans har stöd för flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när den inte längre det senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API, måste du ange API-versionen i varje begäran via parametern api-versionen. När du använder .NET SDK, avgör version av SDK: N som du använder motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Vad är nytt i version 3
Version 3 av Azure Search .NET SDK riktar sig mot senast allmänt tillgängliga versionen av Azure Search-REST-API, särskilt 2016-09-01. Detta gör det möjligt att använda många nya funktioner i Azure Search från .NET-program, inklusive följande:

* [Anpassade analysverktyg](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) stöd för indexerare
* Indexeraren anpassning via [fältmappningar](search-indexer-field-mappings.md)
* ETags som stöd för att aktivera säker samtidig uppdatering av definitioner av index, indexerare och datakällor
* Stöd för att skapa index fältdefinitioner deklarativt genom att dekorera din modellklass och använda den nya `FieldBuilder` klass.
* Stöd för .NET Core och portabla .NET-profil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdatera dina NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

Återskapa ditt projekt när NuGet har laddats ned nya paket och deras beroenden. Beroende på hur din kod är strukturerade, kan den återskapa har. I så, fall är du redo att sätta igång!

Om din version misslyckas, bör du se ett build-fel som liknar följande:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Nästa steg är att åtgärda den här build-fel. Se [större ändringar i version 3](#ListOfChanges) mer information om vad som orsakar felet och hur du åtgärdar den.

Du kan se ytterligare build-varningar relaterade till föråldrade metoder eller egenskaper. Varningar innehåller information om vad du ska använda i stället för föråldrad funktion. Exempel: om programmet använder den `IndexingParameters.Base64EncodeKeys` egenskapen, bör du får ett varningsmeddelande som säger `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

När du har åtgärdat eventuella build-fel, kan du gör ändringar i ditt program för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK finns beskrivna i [Nyheter i version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Större ändringar i version 3
Ett litet antal större ändringar i version 3 som kan kräva kod ändrar det förutom återskapa ditt program.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient returtyp
Den `Indexes.GetClient` metoden har en ny returtyp. Tidigare returnerades `SearchIndexClient`, men detta har ändrats till `ISearchIndexClient` i förhandsversionen med version 2.0 och att ändringen följer med till version 3. Detta är att stödja kunder som vill testa den `GetClient` metod för enhetstester genom att returnera en fingerad implementering av `ISearchIndexClient`.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ändra det till det här för att åtgärda eventuella build-fel:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName och datatyp är inte längre implicit konverteras till strängar
Det finns många typer i Azure Search .NET SDK, som härleds från `ExtensibleEnum`. Tidigare typerna var alla implicit konverteras till typen `string`. Men ett fel upptäcktes i den `Object.Equals` implementeringen för de här klasserna och åtgärda bugg som krävs för inaktivering av den här implicit konvertering. Explicit konvertering till `string` fortfarande tillåts.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Du kan ändra det till det här för att åtgärda eventuella build-fel:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Ta bort föråldrade medlemmar

Du kan se build fel relaterade till metoder eller egenskaper som har markerats som föråldrade i version 2.0 preview och därefter borttagna i version 3. Om du stöter på sådana fel är här hur du löser dem:

- Om du använde den här konstruktorn: `ScoringParameter(string name, string value)`, Använd den här i stället: `ScoringParameter(string name, IEnumerable<string> values)`
- Om du använde den `ScoringParameter.Value` anger den `ScoringParameter.Values` egenskapen eller `ToString` metoden i stället.
- Om du använde den `SearchRequestOptions.RequestId` anger den `ClientRequestId` egenskapen i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsversionsfunktioner

Om du uppgraderar från version 2.0-preview version 3, Tänk på att JSON- och CSV parsning stöd för Blob-indexerare har tagits bort eftersom dessa funktioner är fortfarande i förhandsversion. Mer specifikt kan följande metoder i den `IndexingParametersExtensions` klassen har tagits bort:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om ditt program har ett fast beroende på de här funktionerna kan du inte uppgradera till version 3 av Azure Search .NET SDK. Du kan fortsätta att använda version 2.0-förhandsversion. Men. Tänk på att **rekommenderar vi inte använder förhandsversionen av SDK: er i produktionsprogram**. Funktioner i förhandsversion är för utvärdering endast och kan ändras.

## <a name="conclusion"></a>Sammanfattning
Om du behöver mer information om hur du använder Azure Search .NET SDK kan du läsa den [.NET How-to](search-howto-dotnet-sdk.md).

Vi uppskattar din feedback om SDK. Om du får problem kan passa på att be om hjälp oss på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du har hittat ett programfel kan du rapportera problemet i den [Azure .NET SDK på GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att din Ärenderubrik med ”[Azure Search]”-prefix.

Tack för att använda Azure Search!
