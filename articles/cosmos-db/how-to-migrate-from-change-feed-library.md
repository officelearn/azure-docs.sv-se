---
title: Migrera från ändringsfeedprocessorbiblioteket till Azure Cosmos DB .NET V3 SDK
description: Lär dig hur du migrerar ditt program från att använda ändringsflödesprocessorbiblioteket till Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588891"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrera från ändringsfeedprocessorbiblioteket till Azure Cosmos DB .NET V3 SDK

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [ändringsflödesprocessorbiblioteket](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) till [ändringsfeedfunktionen](change-feed.md) i den senaste versionen av .NET SDK (kallas även .NET V3 SDK).

## <a name="required-code-changes"></a>Nödvändiga kodändringar

.NET V3 SDK har flera brytningsändringar, följande är de viktigaste stegen för att migrera ditt program:

1. Konvertera `DocumentCollectionInfo` instanserna `Container` till referenser för de övervakade och lånebehållarna.
1. Anpassningar som `WithProcessorOptions` används bör uppdateras `WithPollInterval` för att `WithStartTime` använda `WithLeaseConfiguration` och för intervall, [för starttid](how-to-configure-change-feed-start-time.md)och `WithMaxItems` för att definiera det maximala antalet objekt.
1. Ange `processorName` att `GetChangeFeedProcessorBuilder` det ska matcha `ChangeFeedProcessorOptions.LeasePrefix`värdet som `string.Empty` konfigurerats på eller använda något annat.
1. Ändringarna levereras inte längre `IReadOnlyList<Document>`som en , `IReadOnlyCollection<T>` i `T` stället är det en där är en typ som du behöver definiera, det finns ingen basartikelklass längre.
1. För att hantera ändringarna behöver du inte längre en implementering, utan du måste [definiera ett ombud](change-feed-processor.md#implementing-the-change-feed-processor). Ombudet kan vara en statisk funktion eller, om du behöver upprätthålla tillstånd över körningar, kan du skapa din egen klass och skicka en instansmetod som ombud.

Om den ursprungliga koden för att skapa ändringsmatningsbehandlaren till exempel ser ut så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Den migrerade koden kommer att se ut som:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Och delegaten, kan vara en statisk metod:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Tillstånds- och lånebehållare

I likhet med ändringsfeedprocessorbiblioteket använder funktionen för ändringsfeed i .NET V3 SDK en [lånebehållare](change-feed-processor.md#components-of-the-change-feed-processor) för att lagra tillståndet. Schemana är dock olika.

SDK V3-ändringsfeedprocessorn identifierar alla gamla bibliotekstillstånd och migrerar det automatiskt till det nya schemat vid den första körningen av den migrerade programkoden. 

Du kan säkert stoppa programmet med den gamla koden, migrera koden till den nya versionen, starta det migrerade programmet och alla ändringar som inträffade medan programmet stoppades, kommer att plockas upp och bearbetas av den nya versionen.

> [!NOTE]
> Migreringar från program som använder biblioteket till .NET V3 SDK är enkelriktade, eftersom tillståndet (lån) migreras till det nya schemat. Migreringen är inte bakåtkompatibel.


## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användningsexempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödesbehandlare i följande artiklar:

* [Översikt över ändringsmatningsprocessor](change-feed-processor.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](how-to-configure-change-feed-start-time.md)
