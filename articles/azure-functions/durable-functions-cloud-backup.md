---
title: "FAN-in/fan-i scenarier i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar ett fan-in-fläkt-in-scenario i tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: a5d539172f03246e3c658f2485d29d3ae389ae52
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>FAN-in/fan-i scenariot i varaktiga funktioner - molnet säkerhetskopiering exempel

*FAN-in/fan-i* refererar till mönstret för samtidigt köra flera funktioner och sedan utföra vissa aggregering av resultaten. Den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) att implementera ett fan-i/fan-in scenario. Exemplet är en beständig funktion som säkerhetskopierar alla eller vissa av en app webbplatsens innehåll i Azure Storage.

## <a name="prerequisites"></a>Krav

* Följ instruktionerna i [installera varaktiga funktioner](durable-functions-install.md) att ställa in provet.
* Den här artikeln förutsätter att du redan har gått igenom de [Hello sekvens](durable-functions-sequence.md) exempel genomgången.

## <a name="scenario-overview"></a>Scenarioöversikt

I det här exemplet överför funktionerna som alla filer under en angiven katalog rekursivt i blob-lagring. De räkna även det totala antalet byte som har hämtats.

Det går att skriva en funktion som tar hand om allt. Största problemet om du kör i **skalbarhet**. En enskild funktion körning kan bara köra på en enda virtuell dator så genomflödet begränsas av genomströmning av den enda VM. Ett annat problem är **tillförlitlighet**. Om det finns ett fel halva, eller om hela processen tar mer än 5 minuter, kan säkerhetskopieringen misslyckas i ett tillstånd som delvis har slutförts. Det sedan behöver startas om.

En mer robusta metod är att skriva två reguljärt funktioner: en skulle räkna upp filer och Lägg till filnamnen till en kö och en annan skulle läsa från kön och överföra filer till blob storage. Det här är bättre genomflöde och tillförlitlighet, men du måste konfigurera och hantera en kö. Viktigare är betydande komplexitet introduceras i **tillstånd management** och **samordning** om du vill göra något mer det totala antalet byte som överförs som rapporten.

En metod för beständig funktioner får du alla nämnda fördelar med mycket låg belastning.

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

I följande avsnitt beskrivs konfiguration och kod som används för utveckling av Azure portal. Kod för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Molnet säkerhetskopiering orchestration (Visual Studio Code och Azure portal exempelkod)

Den `E2_BackupSiteContent` funktion använder vanlig *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar funktionen orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

Den här funktionen för orchestrator i grunden gör följande:

1. Tar en `rootDirectory` värdet som indataparameter.
2. Anropar en funktion för att få en rekursiv lista över filer under `rootDirectory`.
3. Gör flera parallella funktionsanrop att överföra varje fil i Azure Blob Storage.
4. Väntar på att alla överföringar slutförts.
5. Returnerar summan Totalt antal byte som har överförts till Azure Blob Storage.

Observera den `await Task.WhenAll(tasks);` rad. Alla anrop till den `E2_CopyFileToBlob` funktion har *inte* inväntas. Detta är avsiktligt så att de körs parallellt. När vi skicka denna matris med aktiviteter som `Task.WhenAll`, vi få tillbaka en uppgift som inte slutföra *förrän alla kopieringsåtgärder har slutfört*. Om du är bekant med den uppgiften parallella bibliotek (TPL) i .NET, är detta inte nya för dig. Skillnaden är att dessa uppgifter kan köras på flera virtuella datorer samtidigt och tillägget varaktiga funktioner garanterar att slutpunkt till slutpunkt-körningen är känsligt för processåtervinning.

Efter väntar på från `Task.WhenAll`, vi vet att alla funktionsanrop har slutförts och returnerade värden tillbaka till oss. Varje anrop till `E2_CopyFileToBlob` returnerar antalet byte som överförs, så räknar summan Totalt antal byte är en fråga för att lägga till alla de returvärden tillsammans.

## <a name="helper-activity-functions"></a>Hjälpfunktioner för aktiviteten

Aktiviteten hjälpfunktioner som med andra exempel är vanliga funktioner som använder den `activityTrigger` utlösa bindning. Till exempel den *function.json* för `E2_GetFileList` ser ut som följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Och här är implementering:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> Du kanske undrar varför du bara det gick inte att placera koden direkt till orchestrator-funktionen. Du kan, men det skulle innebära att en av de grundläggande reglerna för orchestrator-funktioner, vilket är att gör de aldrig i/o, inklusive lokal användare.

Den *function.json* för `E2_CopyFileToBlob` är på samma sätt enkel:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Genomförandet är också enkelt. Det händer använder vissa avancerade funktioner i Azure Functions Bindningar (det vill säga användningen av den `Binder` parametern), men du behöver inte bry dig om detaljer för den här genomgången.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

Implementeringen filen läses från disken och strömmar asynkront innehållet i en blob med samma namn i behållaren ”säkerhetskopiering”. Det returnera värdet är antalet byte som kopieras till lagring, som sedan används av orchestrator-funktionen för att beräkna summan.

> [!NOTE]
> Detta är ett bra exempel för att flytta i/o-åtgärder i en `activityTrigger` funktion. Inte bara kan fördelas arbete på många olika virtuella datorer, men du får också fördelarna med kontrollpunkter förloppet. Om värdprocessen hämtar avslutas av någon anledning, vet du vilken överföringar har slutförts.

## <a name="run-the-sample"></a>Köra exemplet

Du kan starta orchestration genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Den `HttpStart` funktion som du anropar fungerar bara med JSON-formaterade innehåll. Därför kan den `Content-Type: application/json` rubrik krävs och katalogsökvägen kodas som en JSON-sträng.

Den här HTTP-begäran utlösare i `E2_BackupSiteContent` orchestrator och överför strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att hämta status för säkerhetskopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Den här åtgärden kan ta flera minuter att slutföra beroende på hur många loggfiler som du har i din funktionsapp. Du kan hämta den senaste statusen genom att fråga en URL i den `Location` rubriken för föregående 202 HTTP-svar.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

I det här fallet körs funktionen fortfarande. Du kan se de indata som sparats i orchestrator-tillstånd och den senast uppdaterades. Du kan fortsätta att använda den `Location` huvudvärden att söka efter slutförande. När status är ”klar” visas en HTTP-Svarsvärde som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Nu kan du se att orchestration är klar och CA hur mycket tid det tog för att slutföra. Se även ett värde för den `output` fältet som anger att cirka 450 KB loggar laddades upp.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-filen i Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar hur du implementerar fan-in/fan-i mönstret. I nästa exempel visas hur du implementerar den [tillståndskänslig singleton](durable-functions-singletons.md) mönstret i en [eternal orchestration](durable-functions-eternal-orchestrations.md).

> [!div class="nextstepaction"]
> [Kör tillståndskänslig singleton-exempel](durable-functions-counter.md)
