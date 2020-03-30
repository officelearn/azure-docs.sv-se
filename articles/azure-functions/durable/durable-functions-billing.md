---
title: Fakturering för varaktiga funktioner - Azure-funktioner
description: Lär dig mer om de interna beteendena för varaktiga funktioner och hur de påverkar faktureringen för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233016"
---
# <a name="durable-functions-billing"></a>Fakturering av varaktiga funktioner

[Varaktiga funktioner](durable-functions-overview.md) faktureras på samma sätt som Azure Functions. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

När du kör orchestrator-funktioner i Azure Functions [Consumption plan](../functions-scale.md#consumption-plan)måste du vara medveten om vissa faktureringsbeteenden. I följande avsnitt beskrivs dessa beteenden och deras effekt mer i detalj.

## <a name="orchestrator-function-replay-billing"></a>Fakturering för Orchestrator-funktion omspel

[Orchestrator-funktioner](durable-functions-orchestrations.md) kan spelas upp flera gånger under en orkestrerings livstid. Varje repris visas av Azure Functions runtime som en distinkt funktionsyrkt. Därför debiteras du i Azure Functions Consumption-planen för varje repris av en orchestrator-funktion. Andra plantyper tar inte betalt för orchestrator-funktionsuppspelning.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Väntar och ger i orchestrator funktioner

När en orchestrator-funktion väntar på att en asynkron åtgärd ska slutföras genom att **använda invänta** i C# eller **ge i** JavaScript, anser körningen att viss körning ska slutföras. Faktureringen för orchestrator-funktionen stoppas vid den punkten. Det återupptas inte förrän nästa orchestrator funktion repris. Du faktureras inte för någon tid i väntan på eller ger i en orchestrator funktion.

> [!NOTE]
> Funktioner som anropar andra funktioner anses av vissa vara ett antimönster. Detta beror på ett problem som kallas _dubbel fakturering_. När en funktion anropar en annan funktion direkt körs båda samtidigt. Den anropade funktionen kör aktivt kod medan anroparfunktionen väntar på ett svar. I det här fallet måste du betala för den tid som samtalsfunktionen spenderar väntar på att den anropade funktionen ska köras.
>
> Det finns ingen dubbel fakturering i orchestrator-funktioner. En orchestrator-funktions fakturering stoppas medan den väntar på resultatet av en aktivitetsfunktion eller underorkestrering.

## <a name="durable-http-polling"></a>Varaktig HTTP-avsökning

Orchestrator-funktioner kan ringa långvariga HTTP-anrop till externa slutpunkter enligt beskrivningen i [http-funktionsartikeln](durable-functions-http-features.md). Metoden **CallHttpAsync** i C# och **metoden callHttp** i JavaScript kan internt avsöka en HTTP-slutpunkt när du följer det [asynkrona 202-mönstret](durable-functions-http-features.md#http-202-handling).

Det finns för närvarande inte direkt fakturering för interna HTTP-avsökningsåtgärder. Interna avsökningar kan dock leda till att orchestrator-funktionen regelbundet spelas upp igen. Du debiteras standardavgifter för dessa interna funktionsrepriser.

## <a name="azure-storage-transactions"></a>Azure Storage-transaktioner

Varaktiga funktioner använder Azure Storage som standard för att hålla tillståndet beständigt, bearbeta meddelanden och hantera partitioner via blob-lån. Eftersom du äger det här lagringskontot debiteras alla transaktionskostnader till din Azure-prenumeration. Mer information om Azure Storage-artefakter som används av varaktiga funktioner finns i [artikeln Aktivitetshubbar](durable-functions-task-hubs.md).

Flera faktorer bidrar till de faktiska Azure Storage-kostnaderna för din varaktiga funktioner-app:

* En enskild funktionsapp är associerad med en enskild aktivitetsnav, som delar en uppsättning Azure Storage-resurser. Dessa resurser används av alla varaktiga funktioner i en funktionsapp. Det faktiska antalet funktioner i funktionsappen påverkar inte Azure Storage-transaktionskostnader.
* Varje funktionsappinstans avsöker internt flera köer i lagringskontot med hjälp av en exponentiell backoff-avsökningsalgoritm. En inaktiv appinstans avsökning av köerna mindre ofta än en aktiv app, vilket resulterar i färre transaktionskostnader. Mer information om kösökningsbeteende för varaktiga funktioner finns i [avsnittet kö-avsökning i artikeln Prestanda och skala](durable-functions-perf-and-scale.md#queue-polling).
* När du kör i Azure Functions Consumption eller Premium-abonnemangen avbÃr er [azure functions-skalningsstyrenheten](../functions-scale.md#how-the-consumption-and-premium-plans-work) avsökningar regelbundet alla arbetshubb-köer i bakgrunden. Om en funktionsapp är i ljus till måttlig skala, kommer endast en enda skala controller instans avsöka dessa köer. Om funktionsappen skalas ut till ett stort antal instanser kan fler skalningsinstanser läggas till. Dessa ytterligare skalningskontrollantinstanser kan öka de totala kötransaktionskostnaderna.
* Varje funktionsappinstans konkurrerar om en uppsättning blob-lån. Dessa instanser kommer regelbundet att ringa till Azure Blob-tjänsten antingen för att förnya ägda lån eller för att försöka förvärva nya lån. Aktivitetshubbens konfigurerade partitionsantal bestämmer antalet blob-lån. Skala ut till ett större antal funktionsappinstanser ökar sannolikt azure storage-transaktionskostnaderna som är associerade med dessa leasingåtgärder.

Du hittar mer information om Azure Storage-priser i [Azure Storage-prisdokumentationen.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om prissättning av Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
