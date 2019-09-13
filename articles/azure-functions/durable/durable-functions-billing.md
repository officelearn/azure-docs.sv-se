---
title: Fakturering av varaktiga funktioner – Azure Functions
description: Lär dig mer om de interna funktionerna i varaktiga funktioner och hur de påverkar faktureringen för Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935889"
---
# <a name="durable-functions-billing"></a>Durable Functions fakturering

[Durable Functions](durable-functions-overview.md) faktureras på samma sätt som Azure Functions. Mer information finns i [priser för Azure Functions](https://azure.microsoft.com/pricing/details/functions/). När du kör Orchestrator Functions i Azure Functions [förbruknings planen](../functions-scale.md#consumption-plan)finns det några fakturerings beteenden som du bör känna till. I följande avsnitt beskrivs dessa beteenden och deras inverkan på mer detaljerat.

## <a name="orchestrator-function-replay-billing"></a>Återuppspelning av Orchestrator-funktion

[Orchestrator-funktioner](durable-functions-orchestrations.md) kan spelas upp flera gånger under dirigeringens livs längd. Varje repetition visas av Azure Functions runtime som ett distinkt funktions anrop. Därför debiteras du för varje repetition av Orchestrator-funktionen i Azure Functions förbruknings planen. Andra typer av abonnemang debiteras inte för att spela upp Orchestrator-funktionen.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Väntar och ger upphov till Orchestrator-funktioner

När en Orchestrator-funktion väntar på att en asynkron åtgärd ska slutföras med `yield` hjälp av `await` (C#) eller (Java Script), anser körningen att en viss körning har slutförts. Faktureringen för Orchestrator-funktionen stoppas vid den punkten och återupptas inte förrän nästa Orchestrator-funktion spelas upp igen. Du debiteras inte för någon tid som ägnats åt att vänta eller ge en Orchestrator-funktion.

> [!NOTE]
> Funktioner som anropar andra funktioner betraktas som en del för att vara ett anti-mönster. Detta beror på ett problem som är känt som _dubbel fakturering_. När en funktion anropar en annan funktion direkt körs båda samtidigt. Anropet kör aktivt kod medan anroparen väntar på ett svar. I så fall måste du betala för den tid som anroparen ägnat åt att vänta på att anropet ska köras. Orchestrator-funktioner påverkas inte av den här dubbla faktureringen eftersom Orchestrator-funktionens fakturering slutar när den väntar på resultatet av en aktivitets funktion (eller under dirigering).

## <a name="durable-http-polling"></a>Beständig HTTP-avsökning

Orchestrator-funktioner kan göra tids krävande HTTP-anrop till externa slut punkter enligt beskrivningen i artikeln [http-funktioner](durable-functions-http-features.md) . Metoden (C#) och `callHttp` metoden (Java Script) kan internt avsöka en http-slutpunkt när den följer det [asynkrona 202-mönstret](durable-functions-http-features.md#http-202-handling). `CallHttpAsync` Det finns för närvarande ingen direkt fakturering för de interna HTTP-avsöknings åtgärderna. Den interna avsökningen kan dock leda till att Orchestrator-funktionen upprepas regelbundet och du debiteras standard avgifter för de här interna funktions omuppspelningarna.

## <a name="azure-storage-transactions"></a>Transaktioner i Azure Storage

Durable Functions använder Azure Storage som standard för beständiga tillstånd, bearbeta meddelanden och hantera partitioner via BLOB-lån. Det här lagrings kontot ägs av dig, så alla transaktionskostnader faktureras till din Azure-prenumeration. Mer information om Azure Storage artefakter som används av Durable Functions finns i artikeln [aktivitets nav](durable-functions-task-hubs.md) .

Flera faktorer bidrar till de faktiska Azure Storage kostnader som uppstår i Durable Functions programmet.

* En enda Function-app är associerad med en enda aktivitets hubb som delar en uppsättning Azure Storage-resurser. Dessa resurser används av alla varaktiga funktioner i en Function-app. Det faktiska antalet funktioner i Function-appen påverkar inte Azure Storage transaktionskostnader.
* Varje Function App-instans avsöker internt flera köer i lagrings kontot med en exponentiell backoff-avsöknings algoritm. En inaktiv program instans avsöker köerna mindre oftare än ett aktivt program, vilket resulterar i färre transaktionskostnader. Mer information om hur du Durable Functions funktionen för att söka efter köer finns i [avsnittet om köns avsökning i artikeln om prestanda och skalning](durable-functions-perf-and-scale.md#queue-polling) .
* När du kör i Azure Functions förbruknings-eller Premium-prenumerationer, avsöker [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) regelbundet alla aktivitets nav köer i bakgrunden. Under lätt till måttlig skala avsöker bara en enskild skalnings kontrolls instans dessa köer. Om Function-appen skalar ut till ett stort antal instanser kan fler instanser av skalnings kontroll läggas till. Dessa ytterligare instanser av en skalnings kontroll kan öka den totala antalet transaktioner i transaktionen.
* Varje Function App-instans konkurrerar om en uppsättning BLOB-lån. Dessa instanser kommer regelbundet att göra anrop till Azure-Blob Service för att förnya kvarhållna lån försöker skaffa nya lån. Antalet BLOB-lån bestäms av aktivitets hubbens konfigurerade antal partitioner. Om du skalar ut till ett större antal funktions program instanser ökar förmodligen Azure Storage transaktionskostnader som är kopplade till dessa leasing åtgärder.

Mer information om Azure Storage priser finns i [Azure Storage prissättnings](https://azure.microsoft.com/pricing/details/storage/) dokumentation.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/)
