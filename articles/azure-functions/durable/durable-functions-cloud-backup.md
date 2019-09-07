---
title: Fläkt-ut-och fläkt scenarier i Durable Functions – Azure
description: Lär dig hur du implementerar ett bläddra in-bläddra ut-scenario i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 81c1279670e786ddaa03946869773121a859d3b7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735244"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Exempel på anti-out/fläkt i scenario i Durable Functions-Cloud backup

*Fläkt-ut-och-fläkt* syftar på mönstret för att köra flera funktioner samtidigt och sedan utföra någon agg regering på resultaten. I den här artikeln beskrivs ett exempel som använder [Durable Functions](durable-functions-overview.md) för att implementera ett scenario med fläkt-in-och fläkt. Exemplet är en varaktig funktion som säkerhetskopierar hela eller delar av en Apps webbplats innehåll till Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

I det här exemplet laddar funktionerna upp alla filer i en angiven katalog rekursivt till Blob Storage. De räknar också det totala antalet byte som har överförts.

Det är möjligt att skriva en enda funktion som tar hand om allt. Det huvudsakliga problemet som du skulle köra i är **skalbarhet**. En enskild funktions körning kan bara köras på en enskild virtuell dator, så data flödet begränsas av genomflödet för den virtuella datorn. Ett annat problem är **tillförlitlighet**. Om det uppstår ett problem halvvägs eller om hela processen tar fler än 5 minuter kan säkerhets kopieringen Miss lyckas i ett delvis slutfört tillstånd. Den måste sedan startas om.

En mer robust metod är att skriva två vanliga funktioner: en skulle räkna upp filerna och lägga till fil namnen i en kö, och en annan skulle läsa från kön och överföra filerna till Blob Storage. Detta är bättre med avseende på data flöde och pålitlighet, men kräver att du etablerar och hanterar en kö. Mer viktig information **finns i användnings villkoren och** **samordningen** om du vill göra allt mer, till exempel rapportera det totala antalet byte som överförs.

En Durable Functions metod ger dig alla angivna förmåner med mycket låg belastning.

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript. Koden för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Moln säkerhets kopierings dirigering (Visual Studio Code och Azure Portal exempel kod)

Funktionen använder standard *funktionen. JSON* för Orchestrator functions. `E2_BackupSiteContent`

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar Orchestrator-funktionen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Den här Orchestrator-funktionen gör i princip följande:

1. Använder ett `rootDirectory` värde som indataparameter.
2. Anropar en funktion för att hämta en rekursiv lista över filer `rootDirectory`under.
3. Gör flera parallella funktions anrop för att ladda upp varje fil till Azure Blob Storage.
4. Väntar på att alla uppladdningar ska slutföras.
5. Returnerar summan av totalt antal byte som överförts till Azure Blob Storage.

Lägg märke `await Task.WhenAll(tasks);` tillC#() `yield context.df.Task.all(tasks);` och (JavaScript-rader). Alla enskilda anrop till `E2_CopyFileToBlob` funktionen förväntades *inte* . Detta är avsiktligt att tillåta att de körs parallellt. När vi skickar dessa uppgifter `Task.WhenAll` till (C#) eller `context.df.Task.all` (Java Script) får vi tillbaka en aktivitet som inte slutförs *förrän alla kopierings åtgärder har slutförts*. Om du är bekant med aktivitets Parallel Library (TPL) i .net eller [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) i Java Script är detta inte nytt för dig. Skillnaden är att dessa aktiviteter kan köras på flera virtuella datorer samtidigt och att Durable Functions-tillägget säkerställer att körningen från slut punkt till slut punkt är elastisk för att bearbeta återvinning.

> [!NOTE]
> Även om aktiviteterna är konceptuellt likartade med JavaScript-löfte, `context.df.Task.all` ska `context.df.Task.any` Orchestrator-funktioner `Promise.race` använda och i stället för `Promise.all` och för att hantera aktivitets parallellisering.

Efter att ha väntat `Task.WhenAll` från (eller getts `context.df.Task.all`från) vet vi att alla funktions anrop har slutförts och har returnerat värdena tillbaka till oss. Varje anrop för `E2_CopyFileToBlob` att returnera antalet byte som har överförts, så att summan av total antalet byte är en del av att lägga till alla dessa retur värden tillsammans.

## <a name="helper-activity-functions"></a>Hjälp aktivitetens funktioner

Funktionerna i hjälp aktiviteten, precis som med andra exempel, är bara vanliga funktioner som använder sig `activityTrigger` av trigger-bindningen. T *. ex. används function. JSON* - `E2_GetFileList` filen för att se ut så här:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Här är implementeringen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

JavaScript-implementeringen `E2_GetFileList` av `readdirp` använder modulen för att rekursivt läsa katalog strukturen.

> [!NOTE]
> Du kanske undrar varför du inte ville använda den här koden direkt i Orchestrator-funktionen. Du kan, men detta skulle bryta en av de grundläggande reglerna för Orchestrator-funktioner, vilket är att de aldrig bör göra I/O, inklusive lokal fil system åtkomst.

*Function. JSON* -filen för `E2_CopyFileToBlob` är på samma sätt enkla:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C# Implementeringen är också ganska enkel. Det händer att använda vissa avancerade funktioner i Azure Functions bindningar (det vill säga användningen av `Binder` parametern), men du behöver inte bekymra dig om dessa uppgifter för den här genom gången.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

JavaScript-implementeringen har inte åtkomst till `Binder` funktionen i Azure Functions, så [Azure Storage SDK för noden](https://github.com/Azure/azure-storage-node) tar sitt ställe.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementeringen läser in filen från disken och strömmar innehållet till en blob med samma namn i behållaren "säkerhets kopiering". Returvärdet är antalet byte som kopieras till Storage, som sedan används av Orchestrator-funktionen för att beräkna mängd summan.

> [!NOTE]
> Detta är ett perfekt exempel på hur du flyttar i/O- `activityTrigger` åtgärder till en funktion. Det går inte bara att distribuera arbetet över flera olika virtuella datorer, men du får också fördelarna med att göra en kontroll punkt. Om värd processen avslutas av någon anledning vet du vilka uppladdningar som redan har slutförts.

## <a name="run-the-sample"></a>Kör exemplet

Du kan starta dirigeringen genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funktionen som du anropar fungerar bara med JSON-formaterat innehåll. Därför `Content-Type: application/json` krävs rubriken och katalog Sök vägen kodas som en JSON-sträng. HTTP-kodfragmentet förutsätter att det finns en post `host.json` i filen som tar bort `api/` standardprefixet från alla URL: er för http-utlösare. Du hittar koden för den här konfigurationen i `host.json` filen i exemplen.

Denna http-begäran utlöser `E2_BackupSiteContent` Orchestrator och skickar strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att hämta status för säkerhets kopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Det kan ta flera minuter att slutföra åtgärden beroende på hur många loggfiler du har i din Function-app. Du kan hämta den senaste statusen genom att fråga URL: en i `Location` rubriken för föregående http 202-svar.

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

I det här fallet körs fortfarande funktionen. Du kan se de inloggade ingångarna som sparats i Orchestrator-läge och senast uppdaterad tid. Du kan fortsätta att använda `Location` rubrik värden för att avsöka efter slutförandet. När statusen är "slutförd" visas ett HTTP-svar som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Nu kan du se att dirigeringen är slutförd och ungefär hur lång tid det tog att slutföra. Du kan också se ett värde för `output` fältet, vilket indikerar att cirka 450 KB av loggarna har laddats upp.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-paketet så att du kan köra exempel koden nedan.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar hur du implementerar bläddra in/bläddra in-mönstret. I nästa exempel visas hur du implementerar övervaknings mönstret med hjälp av [varaktiga timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Kör övervaknings exemplet](durable-functions-monitor.md)