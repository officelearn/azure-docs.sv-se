---
title: Felsök PowerShell Azure Functions lokalt
description: Förstå hur du utvecklar funktioner med hjälp av PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 554b7b7f401ec7cdb1ae08839550b81d797764f2
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530338"
---
# <a name="debug-powershell-azure-functions-locally"></a>Felsök PowerShell Azure Functions lokalt

Azure Functions kan du utveckla dina funktioner som PowerShell-skript.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Du kan felsöka dina PowerShell-funktioner lokalt precis som alla PowerShell-skript med hjälp av utvecklingsverktyg som standard följande:

* [Visual Studio Code](https://code.visualstudio.com/): Microsofts kostnadsfria, lätt och öppen källkod textredigerare med PowerShell-tillägget som erbjuder en fullständig PowerShell-utvecklingsmiljö.
* En PowerShell-konsol: Felsök med hjälp av samma kommandon som du använder för att felsöka andra PowerShell-process.

[Azure Functions Core Tools](functions-run-local.md) har stöd för lokal felsökning av Azure Functions, inklusive PowerShell-funktioner.

## <a name="example-function-app"></a>Exempel-funktionsapp

Funktionsappen som används i den här artikeln har en HTTP-utlöst funktion och har följande filer:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Funktionsappen är ungefär som när du får när du har slutfört den [PowerShell Snabbstart](functions-create-first-function-powershell.md).

Funktionskoden i `run.ps1` ser ut som följande skript:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Definiera attach-punkt

För att felsöka en PowerShell-funktion måste funktionen stoppas av felsökningsprogrammet ska anslutas. Den `Wait-Debugger` cmdlet stoppar körningen och väntar tills felsökningsprogrammet.

Allt du behöver göra är att lägga till ett anrop till den `Wait-Debugger` cmdlet precis ovanför den `if` instruktionen på följande sätt:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Felsökning startar enligt det `if` instruktionen. 

Med `Wait-Debugger` på plats, kan du nu felsöka funktioner med hjälp av Visual Studio Code eller en PowerShell-konsol.

## <a name="debug-in-visual-studio-code"></a>Felsöka i Visual Studio Code

Du måste ha följande tillägg för Visual Studio Code för att felsöka dina PowerShell-funktioner i Visual Studio Code:

* [PowerShell](/powershell/scripting/components/Visual Studio Code/using-Visual Studio Code)
* [Azure Functions](functions-create-first-function-vs-code.md)

Läsa in en befintlig funktionsappsprojekt när du har installerat PowerShell och Azure Functions-tillägg. Du kan också [skapa ett funktionsprojekt](functions-create-first-function-vs-code.md).

>[!NOTE]
> Inte bör ditt projekt har de nödvändiga konfigurationsfilerna, uppmanas du att lägga till dem.

### <a name="start-the-function-app"></a>Starta funktionsappen

Kontrollera att `Wait-Debugger` har angetts i funktionen där du vill koppla felsökaren.  Med `Wait-Debugger` lagts till, kan du felsöka din funktionsapp med hjälp av Visual Studio Code.

Välj den **felsöka** fönstret och sedan **koppla till PowerShell-funktionen**.

![debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Du kan även trycka på F5 för att starta felsökningen.

Starta felsökning åtgärden har följande uppgifter:

* Körningar `func extensions install` i terminalen för att installera några Azure Functions-tillägg som krävs av funktionsappen.
* Körningar `func host start` i terminalen för att starta appen i Functions-värden.
* Koppla felsökaren PowerShell till PowerShell-körningsutrymmet i Functions-körning.

Med funktionsappen som körs, måste en separat PowerShell-konsol för att anropa HTTP-utlöst funktion.

I det här fallet är PowerShell-konsolen klienten. Den `Invoke-RestMethod` används för att utlösa funktionen.

Kör följande kommando i en PowerShell-konsol:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Lägg märke till att ett svar direkt inte returneras. Det beror på `Wait-Debugger` har kopplats debugger och PowerShell körning försattes i break-läge när det betyder att. Detta är på grund av den [BreakAll konceptet](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), vilket beskrivs senare. När du trycker på den `continue` knappen felsökaren nu bryter på raden rätt efter `Wait-Debugger`.

Nu felsökningsprogrammet är ansluten och du kan göra den normala felsökaren åtgärder. Mer information om hur du använder felsökningsprogrammet i Visual Studio Code finns i [officiella dokumentationen](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

När du fortsätta och fullständigt anropa skriptet kan se du att:

* PowerShell-konsolen som gjorde den `Invoke-RestMethod` har returnerat ett resultat
* PowerShell-konsolen i Visual Studio Code för integrerad väntar på att ett skript som ska köras

Efterföljande gånger när du anropar funktionen samma felsökningsprogrammet i PowerShell tillägget bryter rätt efter den `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Felsökning i en PowerShell-konsol

>[!NOTE]
> Det här avsnittet förutsätter att du har läst den [Azure Functions Core Tools docs](functions-run-local.md) och vet hur du använder den `func host start` kommando för att starta din funktionsapp.

Öppna en konsol `cd` till katalogen för funktionsappen och kör sedan följande kommando:

```sh
func host start
```

Med funktionsappen som körs och `Wait-Debugger` på plats, kan du ansluta till processen. Du behöver två mer PowerShell-konsoler.

En av konsolerna fungerar som klienten. Detta kan du anropa `Invoke-RestMethod` som utlöser funktionen. Du kan till exempel köra följande kommando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Lägg märke till att den inte returnerar något svar, vilket är ett resultat av den `Wait-Debugger`. PowerShell-körningsutrymmet väntar nu en felsökare ska anslutas. Nu ska vi hämta som anslutna.

I andra PowerShell-konsolen, kör du följande kommando:

```powershell
Get-PSHostProcessInfo
```

Denna cmdlet returnerar en tabell som ser ut som följande utdata:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Anteckna den `ProcessId` för objekt i tabellen med den `ProcessName` som `dotnet`. Den här processen är din funktionsapp.

Kör följande fragment:

```powershell
# This enters into the the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

När startats felsökningsprogrammet delar och visar något som liknar följande utdata:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Nu kan du har stannat på en brytpunkt i den [PowerShell felsökare](/powershell/module/microsoft.powershell.core/about/about_debuggers). Härifrån kan du göra alla vanliga debug-åtgärder, Stega över, Stega in, fortsätta, avsluta, med mera. Om du vill se en fullständig uppsättning med debug-kommandon som är tillgängliga i konsolen, kör den `h` eller `?` kommandon.

Du kan också ange brytpunkter på den här nivån med den `Set-PSBreakpoint` cmdlet.

När du fortsätta och fullständigt anropa skriptet kan se du att:

* PowerShell-konsolen där du körde `Invoke-RestMethod` nu har returnerat ett resultat.
* PowerShell-konsolen där du körde `Debug-Runspace` väntar på att ett skript som ska köras.

Du kan anropa samma funktion igen (med hjälp av `Invoke-RestMethod` till exempel) och Felsökaren bryter i direkt efter den `Wait-Debugger` kommando.

## <a name="considerations-for-debugging"></a>Överväganden för felsökning

Tänk på följande problem när du felsöker din Functions-kod.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` kan orsaka felsökningsprogrammet att logga in en oväntad plats

PowerShell-tillägget använder `Debug-Runspace`, vilket i sin tur PowerShells `BreakAll` funktionen. Den här funktionen visar PowerShell för att stoppa på det första kommandot som körs. Det här beteendet ger dig möjlighet att ange brytpunkter i felsökt körningsutrymmet.

Azure Functions-körningen kör några kommandon innan du anropar faktiskt din `run.ps1` skript, så det är möjligt att felsökningsprogrammet identisk större inom den `Microsoft.Azure.Functions.PowerShellWorker.psm1` eller `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Den här break uppstår kör den `continue` eller `c` kommando för att hoppa över det här brytpunkt. Sedan stoppa vid den förväntade brytpunkten.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner med hjälp av PowerShell finns [utvecklarguide för Azure Functions PowerShell](functions-reference-powershell.md).
