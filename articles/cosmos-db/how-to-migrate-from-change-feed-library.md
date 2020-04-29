---
title: Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK
description: Lär dig hur du migrerar ditt program från att använda biblioteket Change feed processor till Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588891"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [biblioteket Change feed processor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) till funktionen [ändra feed](change-feed.md) i den senaste versionen av .NET SDK (kallas även .net v3 SDK).

## <a name="required-code-changes"></a>Nödvändiga kod ändringar

.NET v3 SDK har flera viktiga ändringar, följande är de viktigaste stegen för att migrera ditt program:

1. Konvertera `DocumentCollectionInfo` instanserna till `Container` referenser för behållare för övervakade och lån.
1. Anpassningar som används `WithProcessorOptions` bör uppdateras för användning `WithLeaseConfiguration` och `WithPollInterval` intervall, `WithStartTime` [för start tid](how-to-configure-change-feed-start-time.md)och `WithMaxItems` för att definiera maximalt antal objekt.
1. Ställ in `processorName` på `GetChangeFeedProcessorBuilder` på för att matcha värdet som `ChangeFeedProcessorOptions.LeasePrefix`kon figurer ATS `string.Empty` på eller Använd annars.
1. Ändringarna levereras inte längre som en `IReadOnlyList<Document>`, i stället är det `IReadOnlyCollection<T>` där `T` är en typ som du måste definiera, det finns ingen bas objekts klass längre.
1. Om du vill hantera ändringarna behöver du inte längre någon implementering, i stället måste du [definiera ett ombud](change-feed-processor.md#implementing-the-change-feed-processor). Ombudet kan vara en statisk funktion eller, om du behöver underhålla tillstånd över körningar, kan du skapa en egen klass och skicka en instans metod som ombud.

Till exempel, om den ursprungliga koden för att bygga Change feed-processorn ser ut så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Den migrerade koden kommer att se ut så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Och delegaten kan vara en statisk metod:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Tillstånds-och Lease container

Precis som i biblioteket för ändrings matnings processor använder funktionen ändra feed i .NET v3 SDK en [container behållare](change-feed-processor.md#components-of-the-change-feed-processor) för att lagra status. Men scheman är olika.

SDK v3-processen för att ändra feed identifierar alla gamla biblioteks tillstånd och migrerar den till det nya schemat automatiskt vid den första körningen av den migrerade program koden. 

Du kan på ett säkert sätt stoppa programmet med den gamla koden, migrera koden till den nya versionen, starta det migrerade programmet och eventuella ändringar som hände när programmet stoppades, hämtas och bearbetas av den nya versionen.

> [!NOTE]
> Migrering från program som använder biblioteket till .NET v3 SDK är enkelriktad, eftersom tillstånd (lån) kommer att migreras till det nya schemat. Migreringen är inte bakåtkompatibel.


## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändring av flödes processor](change-feed-processor.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](how-to-configure-change-feed-start-time.md)
