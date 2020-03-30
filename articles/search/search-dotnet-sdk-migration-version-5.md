---
title: Uppgradera till Azure Search .NET SDK version 5
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 5 från äldre versioner. Läs om vad som är nytt och vilka kodändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793030"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Uppgradera till Azure Search .NET SDK version 5

Om du använder version 4.0-förhandsversion eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera programmet så att det använder version 5.

En mer allmän genomgång av SDK:s exempel finns i [Så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 5 av Azure Search .NET SDK innehåller vissa ändringar från tidigare versioner. Dessa är oftast mindre, så ändra din kod bör kräva endast minimal ansträngning. Se [Steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 2.0-förhandsversion eller äldre bör du först uppgradera till version 3 och sedan uppgradera till version 5. Instruktioner [finns i Uppgradera till Azure Search .NET SDK version 3.](search-dotnet-sdk-migration.md)
>
> Din Azure Search-tjänstinstans stöder flera REST API-versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar koden för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK avgör den version av SDK du använder motsvarande version av REST API.When using the .NET SDK, the version of the SDK you're using determines the corresponding version of the REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Nyheter i version 5
Version 5 av Azure Search .NET SDK riktar sig till den senaste allmänt tillgängliga versionen av AZURE Search REST API, särskilt 2017-11-11. Detta gör det möjligt att använda nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Synonymer](search-synonyms.md).
* Du kan nu programmatiskt komma åt varningar `Warning` i `IndexerExecutionResult` indexeringskörningshistorik (se egenskapen i [.NET-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) för mer information).
* Stöd för .NET Core 2.
* Ny paketstruktur stöder användning av endast de delar av SDK som du behöver (se [Bryta ändringar i version 5](#ListOfChanges) för mer information).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Uppdatera först din NuGet-referens för `Microsoft.Azure.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. Om så är fallet, är du redo att gå!

Om din version misslyckas bör du se ett byggfel som följande:

    The name 'SuggesterSearchMode' does not exist in the current context

Nästa steg är att åtgärda det här byggfelet. Mer information om vad som orsakar felet och hur du åtgärdar det finns i [Bryta ändringar i version 5.](#ListOfChanges)

Observera att på grund av ändringar i förpackningen till Azure Search .NET SDK måste du återskapa ditt program för att kunna använda version 5. Dessa ändringar beskrivs i [Breaking ändringar i version 5](#ListOfChanges).

Du kan se ytterligare byggvarningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den inaktuella funktionen. Om programmet till exempel `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` använder metoden bör du få en varning om att "Det här beteendet är nu aktiverat som standard, så att anropa den här metoden är inte längre nödvändigt."

När du har åtgärdat eventuella byggfel eller varningar kan du göra ändringar i ditt program för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK [beskrivs i Nyheter i version 5.](#WhatsNew)

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Bryta ändringar i version 5

### <a name="new-package-structure"></a>Ny paketstruktur

Den mest betydande bryta förändring i `Microsoft.Azure.Search` version 5 är att församlingen och dess innehåll har delats upp i fyra separata församlingar som nu distribueras som fyra separata NuGet paket:

 - `Microsoft.Azure.Search`: Det här är ett metapaket som innehåller alla andra Azure Search-paket som beroenden. Om du uppgraderar från en tidigare version av SDK bör det räcka att uppgradera paketet och bygga om för att börja använda den nya versionen.
 - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med Azure Search och du behöver bara fråga eller uppdatera dokument i dina index. Om du också behöver skapa eller uppdatera index, synonymkartor eller `Microsoft.Azure.Search` andra resurser på tjänstnivå använder du paketet i stället.
 - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Search-index, synonymkartor, indexerare, datakällor eller andra resurser på tjänstnivå. Om du bara behöver fråga eller uppdatera dokument `Microsoft.Azure.Search.Data` i dina index använder du paketet i stället. Om du behöver alla funktioner i Azure `Microsoft.Azure.Search` Search använder du paketet i stället.
 - `Microsoft.Azure.Search.Common`: Vanliga typer som behövs av Azure Search .NET-biblioteken. Du bör inte behöva använda detta paket direkt i ditt program; Det är bara tänkt att användas som ett beroende.
 
Denna förändring är tekniskt bryta eftersom många typer flyttades mellan församlingar. Det är därför det är nödvändigt att återskapa ditt program för att uppgradera till version 5 av SDK.

Det finns ett litet antal andra bryta ändringar i version 5 som kan kräva kodändringar förutom att återskapa ditt program.

### <a name="change-to-suggesters"></a>Ändra till förslagshållare 

Konstruktorn `Suggester` har inte `enum` längre `SuggesterSearchMode`någon parameter för . Denna uppräkning hade bara ett värde och var därför överflödig. Om du ser byggfel som ett resultat av detta `SuggesterSearchMode` tar du bara bort referenser till parametern.

### <a name="removed-obsolete-members"></a>Borttagna föråldrade medlemmar

Du kan se byggfel relaterade till metoder eller egenskaper som har markerats som föråldrade i tidigare versioner och som sedan togs bort i version 5. Om du stöter på sådana fel, så här löser du dem:

- Om du använde `IndexingParametersExtensions.IndexStorageMetadataOnly` metoden `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` använder du i stället.
- Om du använde `IndexingParametersExtensions.SkipContent` metoden `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` använder du i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 4.0-förhandsversion till version 5 bör du vara medveten om att JSON-matris- och CSV-tolkningsstöd för Blob-indexerare har tagits bort eftersom dessa funktioner fortfarande är i förhandsversion. Närmare bestämt har följande `IndexingParametersExtensions` metoder för klassen tagits bort:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om ditt program har ett hårt beroende av dessa funktioner kan du inte uppgradera till version 5 av Azure Search .NET SDK. Du kan fortsätta att använda version 4.0-preview. Tänk dock på att vi inte rekommenderar att **du använder förhandsgransknings-SDK:er i produktionsprogram**. Förhandsgranskningsfunktioner är endast till för utvärdering och kan komma att ändras.

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK läser du [.NET How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback på SDK. Om du stöter på problem, gärna be oss om hjälp på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar ett fel kan du lämna in ett problem i [Azure .NET SDK GitHub-databasen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixa din problemtitel med "[Azure Search]".

Tack för att du använder Azure Search!
