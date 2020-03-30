---
title: Uppgradera till Azure Search .NET SDK version 3
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 3 från äldre versioner. Läs om nyheter och vilka kodändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792985"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Uppgradera till Azure Search .NET SDK version 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Om du använder version 2.0-förhandsversion eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera programmet så att det använder version 3.

En mer allmän genomgång av SDK:s exempel finns i [Så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 3 av Azure Search .NET SDK innehåller vissa ändringar från tidigare versioner. Dessa är oftast mindre, så ändra din kod bör kräva endast minimal ansträngning. Se [Steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 1.0.2-förhandsversionen eller äldre bör du först uppgradera till version 1.1 och sedan uppgradera till version 3. Instruktioner [finns i Uppgradera till Azure Search .NET SDK version 1.1.](search-dotnet-sdk-migration-version-1.md)
>
> Din Azure Search-tjänstinstans stöder flera REST API-versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar koden för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK avgör den version av SDK du använder motsvarande version av REST API.When using the .NET SDK, the version of the SDK you're using determines the corresponding version of the REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Nyheter i version 3
Version 3 av Azure Search .NET SDK riktar sig till den senaste allmänt tillgängliga versionen av AZURE Search REST API, särskilt 2016-09-01. Detta gör det möjligt att använda många nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Anpassade analysverktyg](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) indexerer stöd
* Anpassning av indexerare via [fältmappningar](search-indexer-field-mappings.md)
* ETags stöd för att möjliggöra säker samtidig uppdatering av indexdefinitioner, indexerare och datakällor
* Stöd för att bygga indexfältdefinitioner deklarativt genom att `FieldBuilder` dekorera modellklassen och använda den nya klassen.
* Stöd för .NET Core och .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Uppdatera först din NuGet-referens för `Microsoft.Azure.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. Om så är fallet, är du redo att gå!

Om din version misslyckas bör du se ett byggfel som följande:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Nästa steg är att åtgärda det här byggfelet. Se [Bryta ändringar i version 3](#ListOfChanges) för information om vad som orsakar felet och hur du åtgärdar det.

Du kan se ytterligare byggvarningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den inaktuella funktionen. Om ditt program till `IndexingParameters.Base64EncodeKeys` exempel använder egenskapen bör du få en varning om att`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

När du har åtgärdat eventuella byggfel kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK [beskrivs i Nyheter i version 3.](#WhatsNew)

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Bryta ändringar i version 3
Det finns ett litet antal bryta ändringar i version 3 som kan kräva kodändringar förutom att återskapa ditt program.

### <a name="indexesgetclient-return-type"></a>Index.GetClient returtyp
Metoden `Indexes.GetClient` har en ny returtyp. Tidigare returnerades `SearchIndexClient`den , men `ISearchIndexClient` detta ändrades till i version 2.0-preview, och den ändringen överförs till version 3. Detta för att stödja kunder `GetClient` som vill håna metoden för enhetstester genom att returnera en mock implementering av `ISearchIndexClient`.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ändra den till detta för att åtgärda eventuella byggfel:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType och andra är inte längre implicit konverterade till strängar
Det finns många typer i Azure Search .NET `ExtensibleEnum`SDK som härleds från . Tidigare dessa typer var alla `string`implicit konvertibla till typ . Ett fel upptäcktes dock `Object.Equals` i implementeringen för dessa klasser och åtgärdade felet som krävs för att inaktivera den här implicita konverteringen. Explicit konvertering `string` till är fortfarande tillåtet.

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

Du kan ändra den till detta för att åtgärda eventuella byggfel:

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

### <a name="removed-obsolete-members"></a>Borttagna föråldrade medlemmar

Du kan se byggfel relaterade till metoder eller egenskaper som har markerats som föråldrade i version 2.0-förhandsversionen och därefter tagits bort i version 3. Om du stöter på sådana fel, så här löser du dem:

- Om du använde den `ScoringParameter(string name, string value)`här konstruktorn: , använd den här i stället:`ScoringParameter(string name, IEnumerable<string> values)`
- Om du använde `ScoringParameter.Value` egenskapen `ScoringParameter.Values` använder du `ToString` egenskapen eller metoden i stället.
- Om du använde `SearchRequestOptions.RequestId` egenskapen `ClientRequestId` använder du egenskapen i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 2.0-förhandsversionen till version 3 bör du vara medveten om att JSON- och CSV-tolkningsstöd för Blob-indexerare har tagits bort eftersom dessa funktioner fortfarande är i förhandsversion. Närmare bestämt har följande `IndexingParametersExtensions` metoder för klassen tagits bort:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om ditt program har ett hårt beroende av dessa funktioner kan du inte uppgradera till version 3 av Azure Search .NET SDK. Du kan fortsätta att använda version 2.0-preview. Tänk dock på att vi inte rekommenderar att **du använder förhandsgransknings-SDK:er i produktionsprogram**. Förhandsgranskningsfunktioner är endast till för utvärdering och kan komma att ändras.

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK läser du [.NET How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback på SDK. Om du stöter på problem, gärna be oss om hjälp på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar ett fel kan du lämna in ett problem i [Azure .NET SDK GitHub-databasen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixa din problemtitel med "[Azure Search]".

Tack för att du använder Azure Search!
