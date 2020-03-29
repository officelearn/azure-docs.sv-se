---
title: Uppgradera till Azure Search .NET SDK version 9
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 9 från äldre versioner. Läs om vad som är nytt och vilka kodändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793006"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Uppgradera till Azure Search .NET SDK version 9

Om du använder version 7.0-förhandsversion eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera programmet så att det använder version 9.

> [!NOTE]
> Om du vill använda version 8.0-förhandsversion för att utvärdera funktioner som ännu inte är allmänt tillgängliga kan du också följa instruktionerna i den här artikeln för att uppgradera till 8.0-förhandsversioner från tidigare versioner.

En mer allmän genomgång av SDK:s exempel finns i [Så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 9 av Azure Search .NET SDK innehåller många ändringar från tidigare versioner. Vissa av dessa bryter ändringar, men de bör bara kräva relativt små ändringar av din kod. Se [Steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 4.0-förhandsversion eller äldre bör du först uppgradera till version 5 och sedan uppgradera till version 9. Instruktioner [finns i Uppgradera till Azure Search .NET SDK version 5.](search-dotnet-sdk-migration-version-5.md)
>
> Din Azure Search-tjänstinstans stöder flera REST API-versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar koden för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK avgör den version av SDK du använder motsvarande version av REST API.When using the .NET SDK, the version of the SDK you're using determines the corresponding version of the REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Nyheter i version 9
Version 9 av Azure Search .NET SDK riktar sig till den senaste allmänt tillgängliga versionen av AZURE Search REST API, särskilt 2019-05-06. Detta gör det möjligt att använda nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [AI-anrikning](cognitive-search-concept-intro.md) är möjligheten att extrahera text från bilder, blobbar och andra ostrukturerade datakällor – vilket berikar innehållet så att det blir mer sökbart i ett Azure Search-index.
* Stöd för [komplexa typer](search-howto-complex-data-types.md) kan du modellera nästan alla kapslade JSON-struktur i ett Azure Search-index.
* [Komplettera automatiskt](search-autocomplete-tutorial.md) är ett alternativ till **föreslå** API för att implementera sök-som-du-typ beteende. Komplettera automatiskt "avslutar" det ord eller den fras som en användare för närvarande skriver.
* [JsonLines tolkningsläge](search-howto-index-json-blobs.md), en del av Azure Blob-indexering, skapar ett sökdokument per JSON-entitet som är avgränsat med en nyrad.

### <a name="new-preview-features-in-version-80-preview"></a>Nya förhandsgranskningsfunktioner i version 8.0-preview
Version 8.0-förhandsversionen av Azure Search .NET SDK-mål API-version 2017-11-11-Preview. Denna version innehåller alla samma funktioner i version 9, plus:

* [Kundhanterade krypteringsnycklar](search-security-manage-encryption-keys.md) för kryptering på mellanlag är en ny förhandsgranskningsfunktion. Förutom den inbyggda krypteringen i vila som hanteras av Microsoft kan du använda ytterligare ett krypteringslager där du är ensam ägare till nycklarna.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Uppdatera först din NuGet-referens för `Microsoft.Azure.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. Om så är fallet, är du redo att gå!

Om din version misslyckas måste du åtgärda varje byggfel. Mer information om hur du löser varje potentiellt byggfel [finns i Bryta ändringar i version 9.](#ListOfChanges)

Du kan se ytterligare byggvarningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den inaktuella funktionen. Om ditt program till `DataSourceType.DocumentDb` exempel använder egenskapen bör du få en varning om att "Den här medlemmen är inaktuell. Använd CosmosDb istället".

När du har åtgärdat eventuella byggfel eller varningar kan du göra ändringar i ditt program för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK [beskrivs](#WhatsNew)i Nyheter i version 9 .

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Bryta ändringar i version 9

Det finns flera brytande ändringar i version 9 som kan kräva kodändringar förutom att återskapa ditt program.

> [!NOTE]
> Listan över ändringar nedan är inte uttömmande. Vissa ändringar kommer sannolikt inte att resultera i byggfel, men bryter tekniskt eftersom de bryter binär kompatibilitet med sammansättningar som är beroende av tidigare versioner av Azure Search .NET SDK-sammansättningar. Sådana ändringar anges inte nedan. Återskapa ditt program när du uppgraderar till version 9 för att undvika binära kompatibilitetsproblem.

### <a name="immutable-properties"></a>Oföränderliga egenskaper

De offentliga egenskaperna för flera modellklasser är nu oföränderliga. Om du behöver skapa anpassade instanser av dessa klasser för testning kan du använda de nya parameteriserade konstruktörerna:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Ändringar i fält

Klassen `Field` har ändrats nu när den också kan representera komplexa fält.

Följande `bool` egenskaper är nu nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Detta beror på att `null` dessa egenskaper nu måste vara i fråga om komplexa fält. Om du har kod som läser dessa egenskaper `null`måste den vara beredd att hantera . Observera att alla `Field` andra egenskaper har alltid varit och fortsätter att vara `null` nullable, och några av dessa kommer också att vara i fråga om komplexa fält - särskilt följande:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Parameterless konstruktor `Field` av `internal`har gjorts . Från och med `Field` nu kräver varje ett uttryckligt namn och datatyp vid tidpunkten för konstruktionen.

### <a name="simplified-batch-and-results-types"></a>Förenklade batch- och resultattyper

I version 7.0-förhandsversionen och tidigare har de olika klasser som kapslar in grupper av dokument strukturerats i parallella klasshierarkier:

  -  `DocumentSearchResult`och `DocumentSearchResult<T>` ärvt från`DocumentSearchResultBase`
  -  `DocumentSuggestResult`och `DocumentSuggestResult<T>` ärvt från`DocumentSuggestResultBase`
  -  `IndexAction`och `IndexAction<T>` ärvt från`IndexActionBase`
  -  `IndexBatch`och `IndexBatch<T>` ärvt från`IndexBatchBase`
  -  `SearchResult`och `SearchResult<T>` ärvt från`SearchResultBase`
  -  `SuggestResult`och `SuggestResult<T>` ärvt från`SuggestResultBase`

De härledda typerna utan en generisk typparameter var avsedda att användas `Document` i "dynamiskt skrivna" scenarier och antog användning av typen.

Från och med version 8.0-förhandsversionen har basklasserna och de icke-generiska härledda klasserna tagits bort. För dynamiskt skrivna scenarier kan `IndexBatch<Document>` `DocumentSearchResult<Document>`du använda , och så vidare.
 
### <a name="removed-extensibleenum"></a>Borttagen ExtensibleEnum

Basklassen `ExtensibleEnum` har tagits bort. Alla klasser som härleds från den är `AnalyzerName`nu `DataType`structs, till exempel , , och `DataSourceType` till exempel. Deras `Create` metoder har också tagits bort. Du kan bara `Create` ta bort samtal till eftersom dessa typer är implicit konvertibla från strängar. Om det resulterar i kompilatorfel kan du uttryckligen anropa konverteringsoperatorn via cast till disambiguate-typer. Du kan till exempel ändra kod så här:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

till denna:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Egenskaper som innehöll valfria värden för dessa typer skrivs nu uttryckligen som nullable så att de fortsätter att vara valfria.

### <a name="removed-facetresults-and-hithighlights"></a>Borttagna facetresults och hithighlights

`FacetResults` Klasserna `HitHighlights` och har tagits bort. Aspekt resultat är nu `IDictionary<string, IList<FacetResult>>` skrivit som `IDictionary<string, IList<string>>`och slå höjdpunkter som . Ett snabbt sätt att lösa byggfel som introducerades av den här ändringen är att lägga `using` till alias högst upp i varje fil som använder de borttagna typerna. Ett exempel:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Ändra till SynonymMap 

Konstruktorn `SynonymMap` har inte `enum` längre `SynonymMapFormat`någon parameter för . Denna uppräkning hade bara ett värde och var därför överflödig. Om du ser byggfel som ett resultat av detta `SynonymMapFormat` tar du bara bort referenser till parametern.

### <a name="miscellaneous-model-class-changes"></a>Diverse modellklassändringar

Egenskapen `AutocompleteMode` `AutocompleteParameters` för är inte längre nullable. Om du har kod som `null`tilldelar den här egenskapen kan du helt enkelt ta bort den och egenskapen initieras automatiskt till standardvärdet.

Ordningen på parametrarna `IndexAction` till konstruktorn har ändrats nu när konstruktorn genereras automatiskt. Istället för att använda konstruktorn rekommenderar `IndexAction.Upload` `IndexAction.Merge`vi att du använder fabriksmetoderna , och så vidare.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 8.0-förhandsversionen till version 9 bör du tänka på att kryptering med kundhanterade nycklar har tagits bort eftersom den här funktionen fortfarande är i förhandsversion. Specifikt egenskaperna `EncryptionKey` hos `Index` och `SynonymMap` har tagits bort.

Om ditt program har ett hårt beroende av den här funktionen kan du inte uppgradera till version 9 av Azure Search .NET SDK. Du kan fortsätta att använda version 8.0-preview. Tänk dock på att vi inte rekommenderar att **du använder förhandsgransknings-SDK:er i produktionsprogram**. Förhandsgranskningsfunktioner är endast till för utvärdering och kan komma att ändras.

> [!NOTE]
> Om du har skapat krypterade index eller synonymkartor med version 8.0-förhandsversionen av SDK kan du fortfarande använda dem och ändra deras definitioner med version 9 av SDK utan att negativt påverka deras krypteringsstatus. Version 9 av SDK skickar `encryptionKey` inte egenskapen till REST API, och som ett resultat av rest API kommer inte att ändra krypteringsstatus för resursen. 

### <a name="behavioral-change-in-data-retrieval"></a>Beteendeförändring vid datahämtning

Om du använder de "dynamiskt `Search`maskinskrivna" `Suggest`, `Get` eller API:er som returnerar förekomster av typen `Document`, `object[]` bör `string[]`du vara medveten om att de nu avserialisera tomma JSON-matriser till i stället för .

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK läser du [.NET How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback på SDK. Om du stöter på problem, gärna be oss om hjälp på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar ett fel kan du lämna in ett problem i [Azure .NET SDK GitHub-databasen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixa din problemtitel med "[Azure Search]".

Tack för att du använder Azure Search!
