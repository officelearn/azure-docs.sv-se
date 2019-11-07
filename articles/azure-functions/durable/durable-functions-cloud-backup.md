---
title: Fläkt-ut-och fläkt scenarier i Durable Functions – Azure
description: Lär dig hur du implementerar ett antidrivet fläkt scenario i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: e2f1042fe1210fe51ae79b1152e51191e7fb066a
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615017"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Exempel på anti-out/fläkt i scenario i Durable Functions-Cloud backup

*Fläkt-ut-och-fläkt* syftar på mönstret för att köra flera funktioner samtidigt och sedan utföra någon agg regering på resultaten. I den här artikeln beskrivs ett exempel som använder [Durable Functions](durable-functions-overview.md) för att implementera ett scenario med fläkt-in-och fläkt. Exemplet är en varaktig funktion som säkerhetskopierar hela eller delar av en Apps webbplats innehåll till Azure Storage.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

I det här exemplet laddar funktionerna upp alla filer i en angiven katalog rekursivt till Blob Storage. De räknar också det totala antalet byte som har överförts.

Det är möjligt att skriva en enda funktion som tar hand om allt. Det huvudsakliga problemet som du skulle köra i är **skalbarhet**. En enskild funktions körning kan bara köras på en enskild virtuell dator, så data flödet begränsas av genomflödet för den virtuella datorn. Ett annat problem är **tillförlitlighet**. Om det uppstår ett problem halvvägs eller om hela processen tar fler än 5 minuter kan säkerhets kopieringen Miss lyckas i ett delvis slutfört tillstånd. Den måste sedan startas om.

En mer robust metod är att skriva två vanliga funktioner: en skulle räkna upp filerna och lägga till fil namnen i en kö, och en annan skulle läsa från kön och överföra filerna till Blob Storage. Den här metoden är bättre när det gäller data flöde och tillförlitlighet, men kräver att du etablerar och hanterar en kö. Mer viktig information **finns i användnings villkoren och** **samordningen** om du vill göra allt mer, till exempel rapportera det totala antalet byte som överförs.

En Durable Functions metod ger dig alla angivna förmåner med mycket låg belastning.

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript. Koden för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Moln säkerhets kopierings dirigering (Visual Studio Code och Azure Portal exempel kod)

Funktionen `E2_BackupSiteContent` använder standard *funktionen. JSON* för Orchestrator functions.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar Orchestrator-funktionen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Den här Orchestrator-funktionen gör i princip följande:

1. Tar ett `rootDirectory` värde som en indataparameter.
2. Anropar en funktion för att hämta en rekursiv lista över filer under `rootDirectory`.
3. Gör flera parallella funktions anrop för att ladda upp varje fil till Azure Blob Storage.
4. Väntar på att alla uppladdningar ska slutföras.
5. Returnerar summan av totalt antal byte som överförts till Azure Blob Storage.

Lägg märke till radernaC#`await Task.WhenAll(tasks);` () och `yield context.df.Task.all(tasks);` (Java Script). Alla enskilda anrop till funktionen `E2_CopyFileToBlob` har *inte* förväntats, vilket gör att de kan köras parallellt. När vi skickar den här matrisen till `Task.WhenAll` (C#) eller `context.df.Task.all` (Java Script) får vi tillbaka en aktivitet som inte slutförs *förrän alla kopierings åtgärder har slutförts*. Om du är bekant med aktivitets Parallel Library (TPL) i .NET eller [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) i Java Script, är detta inte nytt för dig. Skillnaden är att dessa aktiviteter kan köras på flera virtuella datorer samtidigt och att Durable Functions-tillägget säkerställer att körningen från slut punkt till slut punkt är elastisk för att bearbeta återvinning.

> [!NOTE]
> Även om aktiviteter är konceptuellt likartade med JavaScript-löfte, bör Orchestrator-funktioner använda `context.df.Task.all` och `context.df.Task.any` i stället för `Promise.all` och `Promise.race` för att hantera aktivitets parallellisering.

Efter att ha väntat från `Task.WhenAll` (eller fått från `context.df.Task.all`) vet vi att alla funktions anrop har slutförts och har returnerat värdena tillbaka till oss. Varje anrop till `E2_CopyFileToBlob` returnerar antalet byte som har överförts, så att summan av total antalet byte är en del av att lägga till alla dessa retur värden tillsammans.

## <a name="helper-activity-functions"></a>Hjälp aktivitetens funktioner

Funktionerna i hjälp aktiviteten, precis som med andra exempel, är bara vanliga funktioner som använder bindningen `activityTrigger` trigger. Till exempel ser *Function. JSON* -filen för `E2_GetFileList` ut så här:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Här är implementeringen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

JavaScript-implementeringen av `E2_GetFileList` använder modulen `readdirp` för att rekursivt läsa katalog strukturen.

> [!NOTE]
> Du kanske undrar varför du inte ville använda den här koden direkt i Orchestrator-funktionen. Du kan, men detta skulle bryta en av de grundläggande reglerna för Orchestrator-funktioner, vilket är att de aldrig bör göra I/O, inklusive lokal fil system åtkomst.

*Function. JSON* -filen för `E2_CopyFileToBlob` är på samma sätt enkel:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C# Implementeringen är också enkel. Det händer att använda vissa avancerade funktioner i Azure Functions bindningar (det vill säga användningen av `Binder`-parametern), men du behöver inte bekymra dig om dessa uppgifter för den här genom gången.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

JavaScript-implementeringen har inte åtkomst till den `Binder` funktionen i Azure Functions, så [Azure Storage SDK för noden](https://github.com/Azure/azure-storage-node) tar sitt ställe.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementeringen läser in filen från disken och strömmar innehållet till en blob med samma namn i behållaren "säkerhets kopiering". Returvärdet är antalet byte som kopieras till Storage, som sedan används av Orchestrator-funktionen för att beräkna mängd summan.

> [!NOTE]
> Detta är ett perfekt exempel på hur du flyttar I/O-åtgärder till en `activityTrigger`-funktion. Det går inte bara att distribuera arbetet över flera olika virtuella datorer, men du får också fördelarna med att göra en kontroll punkt. Om värd processen avslutas av någon anledning vet du vilka uppladdningar som redan har slutförts.

## <a name="run-the-sample"></a>Kör exemplet

Du kan starta dirigeringen genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Den `HttpStart` funktionen som du anropar fungerar bara med JSON-formaterat innehåll. Av den anledningen krävs `Content-Type: application/json`-rubriken och katalog Sök vägen är kodad som en JSON-sträng. HTTP-kodfragmentet förutsätter att det finns en post i `host.json`-filen som tar bort standard `api/`-prefixet från alla URL: er för HTTP-utlösare. Du hittar koden för den här konfigurationen i `host.json`-filen i exemplen.

Denna HTTP-begäran utlöser `E2_BackupSiteContent` Orchestrator och skickar strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att hämta status för säkerhets kopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Det kan ta flera minuter att slutföra åtgärden beroende på hur många loggfiler du har i din Function-app. Du kan hämta den senaste statusen genom att fråga URL: en i `Location` rubriken för föregående HTTP 202-svar.

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

I det här fallet körs fortfarande funktionen. Du kan se de inloggade ingångarna som sparats i Orchestrator-läge och senast uppdaterad tid. Du kan fortsätta att använda `Location` rubrik värden för att avsöka efter slutförandet. När statusen är "slutförd" visas ett HTTP-svar som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nu kan du se att dirigeringen är slutförd och ungefär hur lång tid det tog att slutföra. Du kan också se ett värde för fältet `output`, vilket indikerar att cirka 450 KB av loggarna har laddats upp.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-paketet för att köra exempel koden nedan.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du implementerar mönstret för fläkt-out/fläkt i. I nästa exempel visas hur du implementerar övervaknings mönstret med hjälp av [varaktiga timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Kör övervaknings exemplet](durable-functions-monitor.md)