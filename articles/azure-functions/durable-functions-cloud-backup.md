---
title: FAN-in/fan-i scenarier i varaktiga funktioner – Azure
description: Lär dig hur du implementerar ett fan-in-fläkt-in-scenario i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: eec75ad9cf0f568e674b2a4f12d962982f84294f
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092673"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>FAN-in/fan-i scenariot i varaktiga funktioner – Cloud backup-exempel

*FAN-in/fan-i* refererar till mönstret för att köra flera funktioner samtidigt och sedan utför en aggregering på resultatet. Den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) att implementera ett fan-i/fan-in scenario. I exemplet är en hållbar funktion som säkerhetskopierar alla eller några av webbplatsinnehåll för en app till Azure Storage.

## <a name="prerequisites"></a>Förutsättningar

* [Installera varaktiga funktioner](durable-functions-install.md).
* Slutför den [Hello sekvens](durable-functions-sequence.md) genomgången.

## <a name="scenario-overview"></a>Scenarioöversikt

I det här exemplet överför alla filer under en angiven katalog rekursivt till blob-lagring i funktionerna. De räkna även det totala antalet byte som överfördes.

Det går att skriva en enda funktion som tar hand om allt. Det huvudsakliga problemet som du skulle råka är **skalbarhet**. Körning av en enda funktion kan bara köras på en enskild virtuell dator så att dataflödet begränsas av dataflödet för den enda virtuella datorn. Ett annat problem är **tillförlitlighet**. Om det finns ett fel halvvägs eller om hela processen tar mer än 5 minuter, misslyckas säkerhetskopieringen i ett delvis slutförd tillstånd. Det skulle sedan behöva startas om.

En mer stabil metod är att skriva två vanliga funktioner: en skulle räkna upp filerna och Lägg till filnamnen till en kö och en annan skulle läsa från kön och överför filerna till blob storage. Detta är bättre när det gäller dataflöde och tillförlitlighet, men du måste etablera och hantera en kö. Ännu viktigare är betydande komplexiteten introduceras i **tillstånd management** och **samordning** om du vill göra något mer som rapporten det totala antalet byte som överförs.

En metod för varaktiga funktioner ger dig alla nämnda fördelarna med mycket låg belastning.

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

I följande avsnitt beskrivs konfiguration och kod som används för C#-skript. Kod för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Cloud backup orchestration (Visual Studio Code och Azure portal exempelkoden)

Den `E2_BackupSiteContent` funktionen använder standard *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar orchestrator-funktion:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Den här orchestrator-funktion i stort sett gör följande:

1. Tar en `rootDirectory` värdet som indataparameter.
2. Anropar en funktion för att få en rekursiv lista över filer under `rootDirectory`.
3. Gör flera parallella funktionsanrop för att ladda upp varje fil i Azure Blob Storage.
4. Väntar på att alla överföringar att slutföra.
5. Returnerar summan Totalt antal byte som överfördes till Azure Blob Storage.

Observera den `await Task.WhenAll(tasks);` (C#) och `yield context.df.Task.all(tasks);` (JS) rad. Alla anrop till den `E2_CopyFileToBlob` funktion har *inte* slutförts. Detta är avsiktligt så att de kan köras parallellt. När vi skicka den här matrisen med aktiviteter som `Task.WhenAll`, vi få tillbaka en aktivitet som inte slutföra *förrän alla kopieringsåtgärder har slutfört*. Om du är bekant med den uppgiften parallella bibliotek (TPL) i .NET, är detta inte nya för dig. Skillnaden är att dessa uppgifter kan köras på flera virtuella datorer samtidigt och tillägget varaktiga funktioner garanterar att slutpunkt till slutpunkt-körningen är motståndskraftig mot processåtervinning.

Aktiviteter är mycket lika JavaScript begreppet löften. Dock `Promise.all` har några skillnader från `Task.WhenAll`. Begreppet `Task.WhenAll` har porteras över som en del av den `durable-functions` JavaScript-modulen och är exklusivt för den.

När du väntar på från `Task.WhenAll` (eller väjande från `context.df.Task.all`), vi vet att alla funktionsanrop har slutförts och ha returnerat värden tillbaka till oss. Varje anrop till `E2_CopyFileToBlob` returnerar antalet byte som överförts, så att beräkna antal för summan Totalt antal byte är en fråga om att lägga till alla de returvärden tillsammans.

## <a name="helper-activity-functions"></a>Hjälpfunktioner för aktivitet

Aktivitet hjälpfunktioner, precis som med andra exempel är vanliga funktioner som använder den `activityTrigger` utlösa bindning. Till exempel den *function.json* för `E2_GetFileList` ser ut som följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Och här är implementeringen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

JavaScript-implementeringen av `E2_GetFileList` använder den `readdirp` modulen rekursivt läsa katalogstrukturen.

> [!NOTE]
> Du kanske undrar varför du bara kunde inte placera den här koden direkt i orchestrator-funktion. Du kan, men det skulle innebära att en av de grundläggande reglerna orchestrator-funktioner, vilket är att de bör aldrig gör i/o, inklusive systemåtkomst för lokal fil.

Den *function.json* för `E2_CopyFileToBlob` är på samma sätt enkelt:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C#-implementeringen är också enkelt. Det händer använder några avancerade funktioner i Azure Functions-Bindningar (dvs, användningen av den `Binder` parametern), men du behöver inte oroa dig den här informationen i den här genomgången.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

JavaScript-implementeringen har inte åtkomst till den `Binder` funktion i Azure Functions, så den [Azure Storage SDK för Node](https://github.com/Azure/azure-storage-node) dess sker. Observera att SDK: N kräver en `AZURE_STORAGE_CONNECTION_STRING` appinställningen.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementeringen läser in filen från disken och strömmar asynkront innehållet till en blob med samma namn i behållaren ”säkerhetskopiering”. Det returnera värdet är antalet byte som kopieras till lagring, som sedan används av orchestrator-funktion för att beräkna summan.

> [!NOTE]
> Det här är ett bra exempel för att flytta i/o-åtgärder i en `activityTrigger` funktion. Inte bara kan arbetet fördelas på många olika virtuella datorer, men du också få fördelarna med kontrollpunkter förloppet. Om värdprocessen hämtar avslutas av någon anledning, vet du vilken överföringar har slutförts.

## <a name="run-the-sample"></a>Kör exemplet

Du kan börja dirigering genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Den `HttpStart` funktion som du anropar fungerar bara med JSON-formaterade innehållet. Därför måste den `Content-Type: application/json` är obligatoriskt och katalogsökvägen kodas som en JSON-sträng.

Den här HTTP-begäran utlösare den `E2_BackupSiteContent` orchestrator och skickar strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att hämta status för säkerhetskopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Den här åtgärden kan ta flera minuter att slutföra beroende på hur många loggfiler som du har i din funktionsapp. Du kan hämta den senaste statusen genom att fråga URL: en i den `Location` rubrik i föregående HTTP 202-svar.

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

I det här fallet körs funktionen fortfarande. Du ska kunna visa indata som sparades i orchestrator-tillstånd och senast uppdaterad. Du kan fortsätta att använda den `Location` huvudvärden för att söka efter slutförande. När status är ”slutfört”, kan du se ett HTTP-Svarsvärde som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Nu kan du se att dirigering är klar och ungefär hur lång tid det tog för att slutföra. Du ser också ett värde för den `output` fält, vilket betyder att cirka 450 KB loggarna överfördes.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar hur du implementerar fan-in/fan-i mönstret. I nästa exempel visas hur du implementerar mönstret övervakaren med [varaktiga timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Kör exemplet Övervakare](durable-functions-monitor.md)
