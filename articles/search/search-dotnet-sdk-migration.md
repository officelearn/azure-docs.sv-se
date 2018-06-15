---
title: Uppgradera till Azure Search .NET SDK version 3 | Microsoft Docs
description: Uppgradera till Azure Search .NET SDK version 3
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 161d22e0ff4ec4ab28107919a80ecc48cd027967
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793485"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Uppgradera till Azure Search .NET SDK version 3
Om du använder version 2.0 Förhandsgranska eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera ditt program att använda version 3.

En mer allmän genomgång av SDK inklusive exempel finns [hur du använder Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Azure Search .NET SDK-version 3 innehåller vissa ändringar från tidigare versioner. Detta är oftast mindre, så ändrar koden kräver endast minsta möjliga ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 1.0.2-preview eller äldre, bör du uppgradera till version 1.1 först och därefter uppgradera till version 3. Se [uppgradera till Azure Search .NET SDK version 1.1](search-dotnet-sdk-migration-version-1.md) anvisningar.
>
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST-API, måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK, anger versionen av du använder SDK motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Vad är nytt i version 3
Version 3 av .NET SDK för Azure Search riktar sig till senast allmänt tillgänglig version av Azure Search-REST-API, särskilt 2016-09-01. Detta gör det möjligt att använda många nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Anpassade analysverktyg](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) stöd för indexerare
* Indexerare anpassning via [fältet mappningar](search-indexer-field-mappings.md)
* ETags som stöd för att aktivera säker samtidig uppdatering av definitioner av index och indexerare datakällor
* Stöd för att skapa index fältdefinitioner deklarativt för pynta modellklass och använda den nya `FieldBuilder` klass.
* Stöd för .NET Core och portabla .NET-profil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Uppdatera först NuGet-referens för `Microsoft.Azure.Search` NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

När NuGet har laddat ned nya paket och deras beroenden, återskapa projektet. Beroende på hur koden är strukturerad, kan den återskapa har. I så fall, är du redo att sätta igång!

Om din build misslyckas, bör du se ett build-fel som liknar följande:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Nästa steg är att korrigera felet build. Se [bryta ändringar i version 3](#ListOfChanges) för information om vad som orsakar felet och hur du åtgärdar den.

Du kan se ytterligare build-varningar som rör föråldrade metoder eller egenskaper. Varningar innehåller information om vad du ska använda i stället för föråldrad funktion. Om programmet använder till exempel den `IndexingParameters.Base64EncodeKeys` egenskapen som du bör få en varning om att `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

När du har åtgärdat eventuella build-fel, kan du göra ändringar i programmet för att dra nytta av nya funktioner, om du vill. Nya funktioner i SDK beskrivs i [vad är nytt i version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Gör ändringar i version 3
Ett litet antal bryta ändringar i version 3 som kan kräva kod ändrar det förutom återskapar ditt program.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient returtyp
Den `Indexes.GetClient` metoden har en ny returtyp. Tidigare returnerade `SearchIndexClient`, men detta har ändrats till `ISearchIndexClient` i version 2.0-preview och att ändra följer med till version 3. Detta är att stödja kunder som vill mock den `GetClient` metod för kontroller genom att returnera en fingerad implementering av `ISearchIndexClient`.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ändra den till det rätta till eventuella build-fel:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName och datatyp är inte längre implicit omvandlas till strängar
Det finns många typer i Azure Search .NET SDK som härleds från `ExtensibleEnum`. Tidigare var dessa typer av alla implicit omvandlas till typen `string`. Men ett fel upptäcktes i den `Object.Equals` implementering för dessa klasser och åtgärda felet krävs om du inaktiverar den här implicit konvertering. Explicit konvertering till `string` fortfarande är tillåtet.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

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

Du kan ändra den till det rätta till eventuella build-fel:

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

Du kan visa build-fel relaterade till metoder eller egenskaper som har markerats som föråldrade i version 2.0-förhandsgranskning och därefter tas bort i version 3. Om du stöter på dessa fel, är här hur du löser dem:

- Om du använder den här konstruktorn: `ScoringParameter(string name, string value)`, Använd den här i stället: `ScoringParameter(string name, IEnumerable<string> values)`
- Om du använder den `ScoringParameter.Value` anger den `ScoringParameter.Values` egenskapen eller `ToString` metod i stället.
- Om du använder den `SearchRequestOptions.RequestId` anger den `ClientRequestId` egenskapen i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 2.0-preview till version 3, Tänk på att JSON och CSV parsning stöd för Blob indexerare har tagits bort eftersom dessa funktioner är fortfarande under förhandsgranskning. Mer specifikt kan följande metoder för den `IndexingParametersExtensions` klass har tagits bort:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om programmet har en fast beroende på de här funktionerna, kan du inte uppgradera till version 3 av Azure Search .NET SDK. Du kan fortsätta att använda version 2.0-preview. Men du tänka på att **vi rekommenderar inte att använda Förhandsgranska SDK: er i produktionsprogram**. Förhandsgranskningsfunktioner gäller enbart och kan ändras.

## <a name="conclusion"></a>Sammanfattning
Om du vill ha mer information om hur du använder Azure Search .NET SDK finns på [.NET anvisningar](search-howto-dotnet-sdk.md).

Vi uppskattar din feedback om SDK. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Om du hittar ett programfel kan du filen ett problem i den [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixet rubriken problemet med ”[Azure Search]”.

Tack för att använda Azure Search!
