---
title: Fan-out/fan-in-scenarier i varaktiga funktioner - Azure
description: Lär dig hur du implementerar ett fan-out-fan-in-scenario i tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562198"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/fan-in scenario i varaktiga funktioner - Cloud backup exempel

*Fan-out/fan-in* refererar till mönstret för att utföra flera funktioner samtidigt och sedan utföra en viss aggregering på resultaten. I den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) för att implementera ett fan-in/fan-out-scenario. Exemplet är en hållbar funktion som säkerhetskopierar allt eller en del av en apps webbplatsinnehåll i Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

I det här exemplet överför funktionerna alla filer under en angiven katalog rekursivt till blob-lagring. De räknar också det totala antalet byte som har överförts.

Det är möjligt att skriva en enda funktion som tar hand om allt. Det största problemet du skulle stöta på är **skalbarhet**. En enda funktionskörning kan bara köras på en enda virtuell dator, så dataflödet begränsas av dataflödet för den enda virtuella datorn. Ett annat problem är **tillförlitlighet**. Om det finns ett fel halvvägs, eller om hela processen tar mer än 5 minuter, kan säkerhetskopieringen misslyckas i ett delvis slutfört tillstånd. Det skulle då behöva startas om.

En mer robust metod skulle vara att skriva två vanliga funktioner: en skulle räkna upp filerna och lägga till filnamnen i en kö, och en annan skulle läsa från kön och ladda upp filerna till blob lagring. Den här metoden är bättre när det gäller dataflöde och tillförlitlighet, men det kräver att du etablerar och hanterar en kö. Ännu viktigare är att betydande komplexitet införs när det gäller **statlig förvaltning** och **samordning** om du vill göra något mer, som att rapportera det totala antalet uppladdade byte.

En hållbar funktioner strategi ger dig alla de nämnda fördelarna med mycket låg overhead.

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempelappen:

* `E2_BackupSiteContent`: En [orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E2_GetFileList` för att hämta `E2_CopyFileToBlob` en lista med filer som ska säkerhetskopieras och sedan anropar för att säkerhetskopiera varje fil.
* `E2_GetFileList`: En [aktivitetsfunktion](durable-functions-bindings.md#activity-trigger) som returnerar en lista med filer i en katalog.
* `E2_CopyFileToBlob`: En aktivitetsfunktion som säkerhetskopierar en enda fil till Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent orchestrator-funktion

Denna orchestrator funktion i huvudsak gör följande:

1. Tar `rootDirectory` ett värde som en indataparameter.
2. Anropar en funktion för att få en `rootDirectory`rekursiv lista över filer under .
3. Gör flera parallella funktionsanrop för att överföra varje fil till Azure Blob Storage.
4. Väntar på att alla uppladdningar ska slutföras.
5. Returnerar summan av totalt antal byte som har överförts till Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Här är koden som implementerar orchestrator-funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Lägg `await Task.WhenAll(tasks);` märke till linjen. Alla enskilda anrop `E2_CopyFileToBlob` till funktionen var *inte* väntade, vilket gör att de kan köras parallellt. När vi skickar den `Task.WhenAll`här matrisen med uppgifter till får vi tillbaka en uppgift som inte *slutförs förrän alla kopieringsåtgärder har slutförts.* Om du är bekant med Task Parallel Library (TPL) i .NET är detta inte nytt för dig. Skillnaden är att dessa uppgifter kan köras på flera virtuella datorer samtidigt, och tillägget Varaktiga funktioner säkerställer att körningen från slutna till slutna kör kan hanteras.

Efter att `Task.WhenAll`ha väntat från vet vi att alla funktionssamtal har slutförts och har returnerat värden tillbaka till oss. Varje anrop för att `E2_CopyFileToBlob` returnera antalet överförda byte, så att beräkna det totala antalet byte är en fråga om att lägga till alla dessa returvärden tillsammans.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funktionen använder *standardfunktionen.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Här är koden som implementerar orchestrator-funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Lägg `yield context.df.Task.all(tasks);` märke till linjen. Alla enskilda anrop `E2_CopyFileToBlob` till funktionen gavs *inte,* vilket gör att de kan köras parallellt. När vi skickar den `context.df.Task.all`här matrisen med uppgifter till får vi tillbaka en uppgift som inte *slutförs förrän alla kopieringsåtgärder har slutförts.* Om du är [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) bekant med i JavaScript, då detta inte är nytt för dig. Skillnaden är att dessa uppgifter kan köras på flera virtuella datorer samtidigt, och tillägget Varaktiga funktioner säkerställer att körningen från slutna till slutna kör kan hanteras.

> [!NOTE]
> Även om uppgifter är begreppsmässigt liknar JavaScript-löften, bör orchestrator-funktioner `context.df.Task.all` använda och `context.df.Task.any` i stället för `Promise.all` och `Promise.race` hantera aktivitetssamallering.

Efter att `context.df.Task.all`ha gett från vet vi att alla funktionssamtal har slutförts och har returnerat värden tillbaka till oss. Varje anrop för att `E2_CopyFileToBlob` returnera antalet överförda byte, så att beräkna det totala antalet byte är en fråga om att lägga till alla dessa returvärden tillsammans.

---

### <a name="helper-activity-functions"></a>Aktivitetsfunktioner för hjälpare

Hjälpaktivitetsfunktionerna, som med andra exempel, är bara `activityTrigger` vanliga funktioner som använder utlösarbindningen.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList aktivitetsfunktion

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Filen *function.json* `E2_GetFileList` för ser ut så här:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

Och här är genomförandet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funktionen använder `readdirp` modulen (version 2.x) för att rekursivt läsa katalogstrukturen.

---

> [!NOTE]
> Du kanske undrar varför du inte bara kunde sätta denna kod direkt i orchestrator funktion. Du kan, men detta skulle bryta en av de grundläggande reglerna för orchestrator funktioner, vilket är att de aldrig bör göra I / O, inklusive lokala filsystem tillgång. Mer information finns i [Orchestrator funktionskodsbegränsningar](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob aktivitetsfunktion

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-paketet för att köra exempelkoden.

Funktionen använder vissa avancerade funktioner i Azure Functions-bindningar (det vill säga användningen av [ `Binder` parametern),](../functions-dotnet-class-library.md#binding-at-runtime)men du behöver inte oroa dig för dessa detaljer i den här genomgången.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Den *function.json* `E2_CopyFileToBlob` filen för är på samma sätt enkel:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript-implementeringen använder [Azure Storage SDK för nod för](https://github.com/Azure/azure-storage-node) att överföra filerna till Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Implementeringen läser in filen från disken och strömmar asynkront innehållet till en blob med samma namn i behållaren "säkerhetskopior". Returvärdet är antalet byte som kopieras till lagring, som sedan används av orchestrator-funktionen för att beräkna den sammanlagda summan.

> [!NOTE]
> Detta är ett perfekt exempel på att `activityTrigger` flytta I/O-operationer till en funktion. Arbetet kan inte bara distribueras över många olika maskiner, utan du får också fördelarna med att kontrollera förloppet. Om värdprocessen avslutas av någon anledning vet du vilka uppladdningar som redan har slutförts.

## <a name="run-the-sample"></a>Kör exemplet

Du kan starta orkestreringen genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Funktionen `HttpStart` som du anropar fungerar bara med JSON-formaterat innehåll. Därför krävs `Content-Type: application/json` huvudet och katalogsökvägen kodas som en JSON-sträng. Dessutom förutsätter HTTP-kodavsnitt att det finns `host.json` en post i `api/` filen som tar bort standardprefixet från alla HTTP-utlösarfunktioners URL:er. Du hittar markeringen för den `host.json` här konfigurationen i filen i exemplen.

Den här HTTP-begäran utlöser `E2_BackupSiteContent` orchestrator och skickar strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att få status för säkerhetskopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Beroende på hur många loggfiler du har i funktionsappen kan det ta flera minuter att slutföra den här åtgärden. Du kan få den senaste statusen `Location` genom att fråga url:en i huvudet på det tidigare HTTP 202-svaret.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

I det här fallet körs funktionen fortfarande. Du kan se indata som sparades i orchestrator-tillståndet och den senast uppdaterade tiden. Du kan fortsätta `Location` att använda rubrikvärdena för att avsöka för slutförande. När statusen är "Slutförd" visas ett HTTP-svarsvärde som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nu kan du se att orkestreringen är klar och ungefär hur lång tid det tog att slutföra. Du ser också ett `output` värde för fältet, vilket indikerar att cirka 450 KB loggar har överförts.

## <a name="next-steps"></a>Nästa steg

Detta prov har visat hur man genomför fan-out/fan-in mönster. Nästa exempel visar hur du implementerar monitormönstret med [varaktiga timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Kör monitorexemplet](durable-functions-monitor.md)