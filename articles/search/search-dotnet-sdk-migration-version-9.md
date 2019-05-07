---
title: Uppgradera till Azure Search .NET SDK version 9 – Azure Search
description: Migrera kod till Azure Search .NET SDK version 9 från äldre versioner. Läs mer om nyheterna och vilka kodändringar krävs.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f540bc304920073bcd823adcf6c9dd47cb2cf93b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159757"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-9"></a>Uppgradera till Azure Search .NET SDK version 9

Om du använder version 7.0-förhandsversion eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera programmet att använda version 9.

> [!NOTE]
> Om du vill använda version 8.0-preview för att utvärdera funktioner som ännu inte är allmänt tillgänglig, kan du även följa instruktionerna i den här artikeln för att uppgradera till 8.0-förhandsversion från tidigare versioner.

En mer allmän genomgång av SDK inklusive exempel finns i [hur du använder Azure Search från .NET-program](search-howto-dotnet-sdk.md).

Azure Search .NET SDK-version 9 innehåller många ändringar från tidigare versioner. Vissa av dessa större ändringar, men de bör endast kräver relativt små ändringar i koden. Se [stegen för att uppgradera](#UpgradeSteps) anvisningar om hur du ändrar din kod till den nya versionen av SDK.

> [!NOTE]
> Om du använder version 4.0-förhandsversion eller äldre, bör du först uppgradera till version 5 och sedan uppgraderar till version 9. Se [uppgradering till Azure Search .NET SDK version 5](search-dotnet-sdk-migration-version-5.md) anvisningar.
>
> Din Azure Search-tjänstinstans har stöd för flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när den inte längre det senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API, måste du ange API-versionen i varje begäran via parametern api-versionen. När du använder .NET SDK, avgör version av SDK: N som du använder motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Vad är nytt i version 9
Azure Search .NET SDK version 9 riktar sig mot senast allmänt tillgängliga versionen av Azure Search-REST-API, särskilt 2019-05-06. Detta gör det möjligt att använda nya funktioner i Azure Search från .NET-program, inklusive följande:

* [Cognitive Search](cognitive-search-concept-intro.md) är en AI-funktion i Azure Search används för att extrahera text från bilder, blobbar och andra Ostrukturerade datakällor - berikas innehållet så att de blir mer sökbara i ett Azure Search-index.
* Stöd för [komplexa typer](search-howto-complex-data-types.md) kan du modellera nästan alla kapslad JSON-strukturen i ett Azure Search-index.
* [Automatisk komplettering](search-autocomplete-tutorial.md) är ett alternativ till den **föreslå** API för att implementera sökning-som-du-type-beteende. Automatisk komplettering ”är klar” ord eller fraser som en användare är för närvarande för att skriva.
* [JsonLines parsningsläge](search-howto-index-json-blobs.md)är en del av Azure Blob indexering, skapar ett Sök-dokument per JSON-entitet som avgränsas med en ny rad.

### <a name="new-preview-features-in-version-80-preview"></a>Nya förhandsvisningsfunktioner version 8.0-förhandsversion
Version 8.0-förhandsversionen av Azure Search .NET SDK riktar sig mot API version 2017-11-11-förhandsversion. Den här versionen innehåller samma funktioner i version 9, plus:

* [Kundhanterad krypteringsnycklar](search-security-manage-encryption-keys.md) för tjänstsidan kryptering vid vila är en ny förhandsgranskningsfunktion. Förutom de inbyggda kryptering vid vila hanteras av Microsoft, kan du använda ett extra lager av kryptering där du är ensam ägare till nycklarna.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdatera dina NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

Återskapa ditt projekt när NuGet har laddats ned nya paket och deras beroenden. Beroende på hur din kod är strukturerade, kan den återskapa har. I så, fall är du redo att sätta igång!

Om din version misslyckas, kommer du behöva åtgärda varje build-fel. Se [större ändringar i version 9](#ListOfChanges) för information om hur du löser alla potentiella skapa fel.

Du kan se ytterligare build-varningar relaterade till föråldrade metoder eller egenskaper. Varningar innehåller information om vad du ska använda i stället för föråldrad funktion. Exempel: om programmet använder den `DataSourceType.DocumentDb` egenskapen bör du får ett varningsmeddelande som säger ”den här medlemmen är inaktuell. Använd CosmosDb i stället ”.

När du har lösts build-fel eller varningar kan du ändra ditt program att kunna utnyttja nya funktioner om du vill. Nya funktioner i SDK finns beskrivna i [Nyheter i version 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Större ändringar i version 9

Det finns flera ändringar i version 9 som kan kräva ändringar i koden förutom återskapa ditt program.

> [!NOTE]
> En lista över ändringar nedan är inte komplett. Vissa ändringar kommer troligen inte resulterar i build-fel, men tekniskt bryter ned eftersom de bryter binär kompatibilitet med paket som beror på tidigare versioner av Azure Search .NET SDK-sammansättningar. Ändringarna visas inte nedan. Återskapa ditt program när du uppgraderar till version 9 för att undvika eventuella binära kompatibilitetsproblem.

### <a name="making-properties-immutable"></a>Gör egenskaper inte kan ändras

Offentliga egenskaper för flera modellklasser är nu inte kan ändras. Om du vill skapa anpassade instanser av dessa klasser för testning kan kan du använda nya parametriserade konstruktorer:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Ändringar i fältet

Den `Field` klass har ändrats nu när det kan också visa komplexa fält.

Följande `bool` egenskaper kan nu vara null:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Det beror på att de här egenskaperna måste nu vara `null` när det gäller komplicerade fälten. Om du har kod som läser dessa egenskaper, det måste vara förberedd på att hantera `null`. Observera att alla andra egenskaper för `Field` har alltid varit och fortsätta att ha värdet null, och några av dem kommer också att `null` när det gäller komplicerade fälten – särskilt följande:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Parameterlös konstruktor av `Field` har gjorts `internal`. Hädanefter kan varje `Field` kräver en explicit och datatyp vid tidpunkten för konstruktion.

### <a name="simplification-of-batch-and-results-types"></a>Förenkling av distribution av batch-och resultat

I version 7.0-preview och tidigare, har de olika klasser som kapslar in grupper av dokument strukturerade i parallella klasshierarkier:

  -  `DocumentSearchResult` och `DocumentSearchResult<T>` ärvs från `DocumentSearchResultBase`
  -  `DocumentSuggestResult` och `DocumentSuggestResult<T>` ärvs från `DocumentSuggestResultBase`
  -  `IndexAction` och `IndexAction<T>` ärvs från `IndexActionBase`
  -  `IndexBatch` och `IndexBatch<T>` ärvs från `IndexBatchBase`
  -  `SearchResult` och `SearchResult<T>` ärvs från `SearchResultBase`
  -  `SuggestResult` och `SuggestResult<T>` ärvs från `SuggestResultBase`

Härledda typer utan en parameter av generisk typ. har avsett att användas i ”dynamiskt typifierade” scenarier och antas användningen av den `Document` typen.

Från och med version 8.0-preview, har basklasser och härledda klasser för icke-generisk alla tagits bort. Du kan använda för dynamiskt typifierade scenarier `IndexBatch<Document>`, `DocumentSearchResult<Document>`och så vidare.
 
### <a name="removal-of-extensibleenum"></a>Borttagning av ExtensibleEnum

Den `ExtensibleEnum` basklass har tagits bort. Alla klasser som är härledda därifrån finns nu strukturer, till exempel `AnalyzerName`, `DataType`, och `DataSourceType` till exempel. Deras `Create` metoder har också tagits bort. Du kan bara ta bort anrop till `Create` eftersom dessa typer är implicit konverteras från strängar. Om som resulterar i Kompilatorfel anropa du explicit operatorn konvertering via omvandling för att disambiguate typer. Du kan till exempel ändra kod så här:

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

till det här:

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

Egenskaper som hålls valfria värden av typerna är nu uttryckligen har angett som kan ha värdet null så att de fortsätter att vara valfri.

### <a name="removal-of-facetresults-and-hithighlights"></a>Ta bort FacetResults och HitHighlights

Den `FacetResults` och `HitHighlights` klasser har tagits bort. Aspekten resultat nu har skrivits som `IDictionary<string, IList<FacetResult>>` och trycker på markeras som `IDictionary<string, IList<string>>`. Ett snabbt sätt att lösa build felen i den här ändringen är att lägga till `using` alias högst upp på varje fil som använder de borttagna typerna. Exempel:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Ändra till SynonymMap 

Den `SynonymMap` konstruktor har inte längre en `enum` parametern för `SynonymMapFormat`. Den här uppräkningen endast hade ett värde och har därför redundant. Om du ser fel till följd av detta i bygge, helt enkelt ta bort referenser till den `SynonymMapFormat` parametern.

### <a name="miscellaneous-model-class-changes"></a>Diverse Modelländringar för klass

Den `AutocompleteMode` egenskapen för `AutocompleteParameters` är inte längre kan ha värdet null. Om du har kod som tilldelar den här egenskapen till `null`, kan du helt enkelt ta bort den och kommer automatiskt att initiera egenskapen till standardvärdet.

Ordningen på parametrar till den `IndexAction` konstruktor har ändrats nu när den här konstruktorn är automatiskt genererade. Istället för att använda konstruktorn, bör du använda fabriksmetoder `IndexAction.Upload`, `IndexAction.Merge`och så vidare.

### <a name="removed-preview-features"></a>Borttagna förhandsversionsfunktioner

Om du uppgraderar från version 8.0-förhandsversion till version 9, vara medveten om att kryptering med Kundhanterade nycklar har tagits bort eftersom den här funktionen är fortfarande i förhandsversion. Mer specifikt den `EncryptionKey` egenskaperna för `Index` och `SynonymMap` har tagits bort.

Om ditt program har en fast beroende på den här funktionen kan du inte uppgradera till Azure Search .NET SDK version 9. Du kan fortsätta att använda version 8.0-förhandsversion. Men. Tänk på att **rekommenderar vi inte använder förhandsversionen av SDK: er i produktionsprogram**. Funktioner i förhandsversion är för utvärdering endast och kan ändras.

> [!NOTE]
> Om du har skapat krypterade index eller synonym kartor med version 8.0-förhandsversion av SDK, du kan fortfarande använda dem och ändra deras definitioner som använder version 9 av SDK utan att påverka deras krypteringsstatus. Version 9 av SDK inte ska skicka den `encryptionKey` egenskapen till REST API och som ett resultat av REST API inte att ändra krypteringsstatus för resursen. 

### <a name="behavioral-change-in-data-retrieval"></a>Ändrat funktionssätt i datahämtning

Om du använder det ”dynamiskt skrivna” `Search`, `Suggest`, eller `Get` API: er som instanser av typen returneras `Document`, Tänk på att de nu deserialiseras tom JSON-matriser till att `object[]` i stället för `string[]`.

## <a name="conclusion"></a>Sammanfattning
Om du behöver mer information om hur du använder Azure Search .NET SDK kan du läsa den [.NET How-to](search-howto-dotnet-sdk.md).

Vi uppskattar din feedback om SDK. Om du får problem kan passa på att be om hjälp oss på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du har hittat ett programfel kan du rapportera problemet i den [Azure .NET SDK på GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att din Ärenderubrik med ”[Azure Search]”-prefix.

Tack för att använda Azure Search!
