---
title: Felsök PowerShell Azure-funktioner lokalt
description: Förstå hur du utvecklar funktioner med hjälp av PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163768"
---
# <a name="debug-powershell-azure-functions-locally"></a>Felsök PowerShell Azure-funktioner lokalt

Med Azure Functions kan du utveckla dina funktioner som PowerShell-skript.

Du kan felsöka dina PowerShell-funktioner lokalt på samma sätt som alla PowerShell-skript med följande standardutvecklingsverktyg:

* [Visual Studio-kod:](https://code.visualstudio.com/)Microsofts kostnadsfria, lätta och öppna källkodstexter med PowerShell-tillägget som erbjuder en fullständig PowerShell-utvecklingsupplevelse.
* En PowerShell-konsol: Felsök med samma kommandon som du använder för att felsöka andra PowerShell-processer.

[Azure Functions Core Tools](functions-run-local.md) stöder lokal felsökning av Azure-funktioner, inklusive PowerShell-funktioner.

## <a name="example-function-app"></a>Exempel funktion app

Funktionsappen som används i den här artikeln har en enda HTTP-utlöst funktion och har följande filer:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Den här funktionsappen liknar den du får när du slutför [Snabbstarten](functions-create-first-function-powershell.md)för PowerShell .

Funktionskoden `run.ps1` i ser ut som följande skript:

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

## <a name="set-the-attach-point"></a>Ställ in fästpunkten

Om du vill felsöka en PowerShell-funktion måste funktionen stoppas för att felsökaren ska kunna anslutas. Cmdleten `Wait-Debugger` stoppar körningen och väntar på felsökaren.

Allt du behöver göra är att `Wait-Debugger` lägga till ett `if` samtal till cmdlet strax ovanför uttalandet, enligt följande:

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

Felsökning börjar vid `if` uttrycket. 

Med `Wait-Debugger` på plats kan du nu felsöka funktionerna med antingen Visual Studio-kod eller en PowerShell-konsol.

## <a name="debug-in-visual-studio-code"></a>Felsöka i Visual Studio-kod

Om du vill felsöka Dina PowerShell-funktioner i Visual Studio-kod måste du ha följande installerat:

* [PowerShell-tillägg för Visual Studio-kod](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-tillägg för Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 eller senare](/powershell/scripting/install/installing-powershell-core-on-windows)

När du har installerat dessa beroenden läser du in ett befintligt PowerShell Functions-projekt eller [skapar ditt första PowerShell Functions-projekt](functions-create-first-function-powershell.md).

>[!NOTE]
> Om projektet inte har de konfigurationsfiler som behövs uppmanas du att lägga till dem.

### <a name="set-the-powershell-version"></a>Ange PowerShell-versionen

PowerShell Core installeras sida vid sida med Windows PowerShell. Ange att PowerShell Core ska användas som PowerShell-version med PowerShell-tillägget för Visual Studio-kod.

1. Tryck på F1 för att visa `Session`kommandopallen och sök sedan efter .

1. Välj **PowerShell: Visa sessionsmeny**.

1. Om din **aktuella session** inte är **PowerShell Core 6**väljer du **Växla till: PowerShell Core 6**.

När du har en PowerShell-fil öppen visas den version som visas i grönt längst ned till höger i fönstret. Om du markerar den här texten visas även sessionsmenyn. Mer information finns i [välja en version av PowerShell som ska användas med tillägget](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Starta funktionsappen

Kontrollera `Wait-Debugger` att det är inställt i funktionen där du vill koppla felsökningsprogrammet.  Med `Wait-Debugger` tillagd kan du felsöka din funktionsapp med Visual Studio-kod.

Välj **felsökningsfönstret** och anslut sedan **till PowerShell-funktionen**.

![Debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Du kan också trycka på F5 för att börja felsöka.

Åtgärden för att starta felsökning utför följande uppgifter:

* Körs `func extensions install` i terminalen för att installera alla Azure Functions-tillägg som krävs av din funktionsapp.
* Körs `func host start` i terminalen för att starta funktionsappen i functions-värden.
* Koppla PowerShell-felsökaren till PowerShell-runspace i funktionskörningen.

>[!NOTE]
> Du måste se till att PSWorkerInProcConcurrencyUpperBound är inställt på 1 för att säkerställa korrekt felsökningsupplevelse i Visual Studio-kod. Det här är standard.

När funktionsappen körs behöver du en separat PowerShell-konsol för att anropa http-utlöst funktion.

I det här fallet är PowerShell-konsolen klienten. Används `Invoke-RestMethod` för att utlösa funktionen.

I en PowerShell-konsol kör du följande kommando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Du kommer att märka att ett svar inte omedelbart returneras. Det beror `Wait-Debugger` på att har bifogat felsöknings- och PowerShell-körningen gick in i pausläge så fort det kunde. Detta beror på [BreakAll konceptet](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), som förklaras senare. När du `continue` har tryckt på knappen bryts felsökningen `Wait-Debugger`nu på linjen direkt efter .

Vid denna punkt är felsökaren ansluten och du kan göra alla normala felsökningsåtgärder. Mer information om hur du använder felsökningen i Visual Studio-koden finns [i den officiella dokumentationen](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

När du har fortsatt och helt anropat skriptet kommer du att märka att:

* PowerShell-konsolen som `Invoke-RestMethod` gjorde det har returnerat ett resultat
* Den integrerade PowerShell-konsolen i Visual Studio-koden väntar på att ett skript ska köras

Senare när du anropar samma funktion bryts felsökningen i PowerShell-tillägget direkt efter `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Felsöka i en PowerShell-konsol

>[!NOTE]
> Det här avsnittet förutsätter att du har läst Azure Functions `func host start` Core [Tools-dokument](functions-run-local.md) och vet hur du använder kommandot för att starta din funktionsapp.

Öppna en konsol `cd` i katalogen för funktionsappen och kör följande kommando:

```sh
func host start
```

Med funktionsappen `Wait-Debugger` igång och på plats kan du koppla till processen. Du behöver ytterligare två PowerShell-konsoler.

En av konsolerna fungerar som klient. Från detta ringer `Invoke-RestMethod` du för att utlösa funktionen. Du kan till exempel köra följande kommando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Du kommer att märka att det inte returnerar ett `Wait-Debugger`svar, vilket är ett resultat av . PowerShell-runspace väntar nu på att en felsökare ska bifogas. Nu sätter vi dit den.

I den andra PowerShell-konsolen kör du följande kommando:

```powershell
Get-PSHostProcessInfo
```

Den här cmdleten returnerar en tabell som ser ut som följande utdata:

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

Anteckna `ProcessId` för objektet i tabellen med `ProcessName` `dotnet`som . Den här processen är din funktionsapp.

Kör sedan följande utdrag:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

När felsökaren har startats bryts och visas ungefär följande utdata:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Nu stoppas du vid en brytpunkt i [PowerShell-felsökningen](/powershell/module/microsoft.powershell.core/about/about_debuggers). Härifrån kan du göra alla vanliga felsökningsåtgärder, kliva över, kliva in, fortsätta, avsluta och andra. Om du vill se den fullständiga uppsättningen felsökningskommandon som är tillgängliga i konsolen kör `h` du kommandona eller. `?`

Du kan också ange brytpunkter på `Set-PSBreakpoint` den här nivån med cmdleten.

När du har fortsatt och helt anropat skriptet märker du att:

* PowerShell-konsolen där du `Invoke-RestMethod` körde har nu returnerat ett resultat.
* PowerShell-konsolen där du `Debug-Runspace` körde väntar på att ett skript ska köras.

Du kan anropa samma funktion `Invoke-RestMethod` igen (till exempel med hjälp av `Wait-Debugger` felsökaren) och felsökaren bryter in direkt efter kommandot.

## <a name="considerations-for-debugging"></a>Överväganden för felsökning

Tänk på följande problem när du felsöker funktionskoden.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`kan orsaka att felsökaren bryts på en oväntad plats

PowerShell-tillägget `Debug-Runspace`använder , som i sin tur `BreakAll` är beroende av PowerShells funktion. Den här funktionen talar om för PowerShell att stanna vid det första kommandot som körs. Detta ger dig möjlighet att ange brytpunkter inom det avuggade runspace.This behavior gives you the opportunity to set breakpoints within the debugged runspace.

Azure Functions-körningen kör några kommandon innan `run.ps1` du anropar skriptet, så det är möjligt `Microsoft.Azure.Functions.PowerShellWorker.psm1` att `Microsoft.Azure.Functions.PowerShellWorker.psd1`felsökaren slutar brytas inom eller .

Om den här `continue` pausen `c` inträffar kör du kommandot eller kommandot för att hoppa över den här brytpunkten. Du stannar sedan vid den förväntade brytpunkten.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner med PowerShell finns i [utvecklarhandboken för Azure Functions PowerShell](functions-reference-powershell.md).
