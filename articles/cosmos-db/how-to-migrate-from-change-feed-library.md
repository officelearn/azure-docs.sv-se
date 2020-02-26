---
title: Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK
description: Lär dig hur du migrerar ditt program från att använda biblioteket Change feed processor till Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: f651beb181430f65d0b4c86f285e74958f8366eb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588891"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [biblioteket Change feed processor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) till funktionen [ändra feed](change-feed.md) i den senaste versionen av .NET SDK (kallas även .net v3 SDK).

## <a name="required-code-changes"></a>Nödvändiga kod ändringar

.NET v3 SDK har flera viktiga ändringar, följande är de viktigaste stegen för att migrera ditt program:

1. Konvertera `DocumentCollectionInfo` instanser till `Container` referenser för behållare för övervakade och lån.
1. Anpassningar som använder `WithProcessorOptions` bör uppdateras för att använda `WithLeaseConfiguration` och `WithPollInterval` för intervall, `WithStartTime` [för start tid](how-to-configure-change-feed-start-time.md)och `WithMaxItems` för att definiera maximalt antal objekt.
1. Ange `processorName` för `GetChangeFeedProcessorBuilder` för att matcha värdet som kon figurer ATS på `ChangeFeedProcessorOptions.LeasePrefix`eller Använd `string.Empty` på annat sätt.
1. Ändringarna levereras inte längre som en `IReadOnlyList<Document>`. i stället är det en `IReadOnlyCollection<T>` där `T` är en typ som du måste definiera. det finns ingen bas objekt klass längre.
1. Om du vill hantera ändringarna behöver du inte längre någon implementering, i stället måste du [definiera ett ombud](change-feed-processor.md#implementing-the-change-feed-processor). Ombudet kan vara en statisk funktion eller, om du behöver underhålla tillstånd över körningar, kan du skapa en egen klass och skicka en instans metod som ombud.

Till exempel, om den ursprungliga koden för att bygga Change feed-processorn ser ut så här:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorLibrary":::

Den migrerade koden kommer att se ut så här:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorMigrated":::

Och delegaten kan vara en statisk metod:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="Delegate":::

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
* [Använda uppskattningen ändra feed](how-to-use-change-feed-estimator.md)
* [Ändra start tid för matnings processor](how-to-configure-change-feed-start-time.md)
