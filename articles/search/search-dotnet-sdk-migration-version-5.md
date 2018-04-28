---
title: Uppgradera till Azure Search .NET SDK version 5 | Microsoft Docs
description: Uppgradera till Azure Search .NET SDK version 5
author: brjohnstmsft
manager: jlembicz
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 018388cd2bd85eb86ad7b62ee247bccd6329e9ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Uppgradera till Azure Search .NET SDK version 5
Om du använder version 4.0 Förhandsgranska eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera ditt program att använda version 5.

En mer allmän genomgång av SDK inklusive exempel finns [hur du använder Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Azure Search .NET SDK-version 5 innehåller vissa ändringar från tidigare versioner. Detta är oftast mindre, så ändrar koden kräver endast minsta möjliga ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 2.0 Förhandsgranska eller äldre bör du först uppgradera till version 3 och sedan uppgradera till version 5. Se [uppgradera till Azure Search .NET SDK version 3](search-dotnet-sdk-migration.md) anvisningar.
>
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST-API, måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK, anger versionen av du använder SDK motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Vad är nytt i version 5
Azure Search .NET SDK version 5 riktar sig till senast allmänt tillgänglig version av Azure Search-REST-API, särskilt 2017-11-11. Detta gör det möjligt att använda nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Synonymer](search-synonyms.md).
* Du kan nu komma åt varningar i indexeraren körningstiden (finns i `Warning` -egenskapen för `IndexerExecutionResult` i den [.NET-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) för mer information).
* Stöd för .NET Core 2.
* Nya paket strukturen stöder bara delarna av SDK: N som du behöver (se [bryta ändringar i version 5](#ListOfChanges) information).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Uppdatera först NuGet-referens för `Microsoft.Azure.Search` NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

När NuGet har laddat ned nya paket och deras beroenden, återskapa projektet. Om du inte använder en förhandsvisningsfunktion som inte är i nya GA SDK den bör återskapa har (om inte, låt oss veta på [GitHub](https://github.com/azure/azure-sdk-for-net/issues)). I så fall, är du redo att sätta igång!

Observera att på grund av ändringar i Azure Search .NET SDK-paketering, måste du återskapa ditt program för att kunna använda version 5. De här ändringarna beskrivs i [bryta ändringar i version 5](#ListOfChanges).

Du kan se ytterligare build-varningar som rör föråldrade metoder eller egenskaper. Varningar innehåller information om vad du ska använda i stället för föråldrad funktion. Om programmet använder till exempel den `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metoden som du bör få en varning som säger ”det här beteendet är nu aktiverad som standard, så anropar den här metoden är inte längre nödvändigt”.

När du har åtgärdat eventuella build-varningar, kan du göra ändringar i programmet för att dra nytta av nya funktioner, om du vill. Nya funktioner i SDK beskrivs i [vad är nytt i version 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Gör ändringar i version 5
Den mest betydande bryta förändringen i version 5 är att den `Microsoft.Azure.Search` sammansättningen och dess innehåll har delats upp i fyra separata sammansättningar som distribueras nu som fyra separata NuGet-paket:

 - `Microsoft.Azure.Search`: Det här är ett meta-paket som innehåller alla andra Azure Search paket som beroenden. Om du uppgraderar från en tidigare version av SDK bör bara uppgradera det här paketet och bygga vara tillräckligt för att börja använda den nya versionen.
 - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med Azure Search och du behöver bara fråga eller uppdatera dokument i ditt index. Om du också behöva skapa eller uppdatera index synonymen maps och andra resurser för servicenivåer använder den `Microsoft.Azure.Search` paketet i stället.
 - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Search index synonymen maps, indexerare, datakällor eller andra resurser på tjänstnivå. Om du behöver bara fråga eller uppdatering dokument i ditt index, använder du den `Microsoft.Azure.Search.Data` paketet i stället. Om du behöver alla funktioner i Azure Search kan du använda den `Microsoft.Azure.Search` paketet i stället.
- `Microsoft.Azure.Search.Common`: Vanliga typer som krävs av Azure Search .NET-bibliotek. Du behöver inte använda det här paketet direkt i ditt program. Det är endast avsett att användas som ett beroende.
 
Den här ändringen tekniskt bryter eftersom många typer har flyttats mellan sammansättningarna. Det är därför återskapa ditt program krävs för att uppgradera till version 5 av SDK.

Ett litet antal andra ändringar i version 5 senaste som kan kräva kod ändrar det förutom återskapar ditt program.

### <a name="removed-obsolete-members"></a>Ta bort föråldrade medlemmar

Du kan se Skapa fel som rör metoder eller egenskaper som har markerats som föråldrade i tidigare versioner och därefter tas bort i version 5. Om du stöter på dessa fel, är här hur du löser dem:

- Om du använder den `IndexingParametersExtensions.IndexStorageMetadataOnly` metod, Använd `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` i stället.
- Om du använder den `IndexingParametersExtensions.SkipContent` metod, Använd `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 4.0-preview till version 5, Tänk på att JSON-matris och CSV parsning stöd för Blob indexerare har tagits bort eftersom dessa funktioner är fortfarande under förhandsgranskning. Mer specifikt kan följande metoder för den `IndexingParametersExtensions` klass har tagits bort:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om programmet har en fast beroende på de här funktionerna, kan du inte uppgradera till version 5 av Azure Search .NET SDK. Du kan fortsätta att använda version 4.0-preview. Men du tänka på att **vi rekommenderar inte att använda Förhandsgranska SDK: er i produktionsprogram**. Förhandsgranskningsfunktioner gäller enbart och kan ändras.

## <a name="conclusion"></a>Sammanfattning
Om du vill ha mer information om hur du använder Azure Search .NET SDK finns på [.NET anvisningar](search-howto-dotnet-sdk.md).

Vi uppskattar din feedback om SDK. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Om du hittar ett programfel kan du filen ett problem i den [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixet rubriken problemet med ”[Azure Search]”.

Tack för att använda Azure Search!
