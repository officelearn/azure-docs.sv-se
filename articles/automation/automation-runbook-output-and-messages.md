---
title: Runbook-utdata och meddelanden i Azure Automation
description: Beskriver hur du skapar och hämtar utdata och felmeddelanden från runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535544"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-utdata och meddelanden i Azure Automation

De flesta Azure Automation-runbooks har någon form av utdata. Den här utdata kan vara ett felmeddelande till användaren eller ett komplext objekt som är avsett att användas med en annan runbook. Windows PowerShell innehåller [flera strömmar](/powershell/module/microsoft.powershell.core/about/about_redirection) för att skicka utdata från ett skript eller ett arbetsflöde. Azure Automation fungerar med var och en av dessa strömmar på olika sätt. Du bör följa metodtips för att använda strömmar när du skapar en runbook.

I följande tabell beskrivs kortfattat varje ström med dess beteende i Azure-portalen för publicerade runbooks och under [testning av en runbook](automation-testing-runbook.md). Utdataströmmen är huvudströmmen som används för kommunikation mellan runbooks. De andra strömmarna klassificeras som meddelandeströmmar, avsedda att kommunicera information till användaren. 

| Strömma | Beskrivning | Publicerad | Testa |
|:--- |:--- |:--- |:--- |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter runbooken efter ett felmeddelande som standard. |Skrivet till jobbhistorik |Visas i fönstret Testutdata |
| Felsökning |Meddelanden avsedda för en interaktiv användare. Bör inte användas i runbooks. |Inte skrivet till jobbhistorik |Visas inte i fönstret Testutdata |
| Resultat |Objekt som ska användas av andra Runbooks. |Skrivet till jobbhistorik |Visas i fönstret Testutdata |
| Förlopp |Poster som genereras automatiskt före och efter varje aktivitet i Runbooken. Runbooken bör inte försöka skapa sina egna förloppsposter, eftersom de är avsedda för en interaktiv användare. |Skrivet till jobbhistorik endast om förloppsloggning är aktiverat för runbooken |Visas inte i fönstret Testutdata |
| Verbose |Meddelanden som ger allmän eller felsökningsinformation. |Skrivet till jobbhistorik endast om utförlig loggning är aktiverad för runbooken |Visas bara i fönstret `VerbosePreference` Testa utdata om variabeln är inställd på Fortsätt i runbook |
| Varning |Varningsmeddelande avsett för användaren. |Skrivet till jobbhistorik |Visas i fönstret Testutdata |

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="output-stream"></a>Utdataström

Utdataströmmen används för utdata från objekt som skapas av ett skript eller ett arbetsflöde när den körs korrekt. Azure Automation använder främst den här strömmen för objekt som ska förbrukas av överordnade runbooks som anropar den [aktuella runbooken](automation-child-runbooks.md). När en överordnad [anropar en runbook infogad](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)returnerar den underordnade data från utdataströmmen till den överordnade. 

Runbook använder utdataströmmen för att kommunicera allmän information till klienten endast om den aldrig anropas av en annan runbook. Som bästa praxis bör du dock köra böcker vanligtvis använda [verbosströmmen](#verbose-stream) för att kommunicera allmän information till användaren.

Låt runbook skriva data till utdataströmmen med hjälp av [write-output](https://technet.microsoft.com/library/hh849921.aspx). Du kan också placera objektet på en egen rad i skriptet.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Hantera utdata från en funktion

När en runbook-funktion skrivs till utdataströmmen skickas utdata tillbaka till runbooken. Om runbooken tilldelar utdata till en variabel skrivs inte utdata till utdataströmmen. Skriva till andra strömmar inifrån funktionen skriver till motsvarande ström för runbook. Tänk på följande exempel på PowerShell Workflow runbook.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Utdataströmmen för runbook-jobbet är:

```output
Output inside of function
Output outside of function
```

Den utförliga strömmen för runbook-jobbet är:

```output
Verbose outside of function
Verbose inside of function
```

När du har publicerat runbooken och innan du startar den måste du också aktivera utförlig loggning i runbook-inställningarna för att få utdata från Utförlig ström.

### <a name="declaring-output-data-type"></a>Deklarera utdatatyp

Följande är exempel på utdatatyper:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarera utdatatyp i ett arbetsflöde

Ett arbetsflöde anger datatypen för utdata med attributet [OutputType](https://technet.microsoft.com/library/hh847785.aspx). Det här attributet har ingen effekt under körning, men det ger dig en indikation vid designtillfället för den förväntade utdata för runbooken. I takt med att verktygsuppsättningen för runbooks fortsätter att utvecklas ökar vikten av att deklarera utdatatyper vid designtid. Därför är det en bra idé att inkludera den här deklarationen i alla runbooks som du skapar.

Följande exempel-Runbook matar ut ett strängobjekt och innehåller en förklaring av utdatatypen. Om din Runbook matar ut en matris av en viss typ bör du fortfarande ange typen, till skillnad från en matris av typen.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarera utdatatyp i en grafisk runbook

Om du vill deklarera en utdatatyp i en grafisk eller grafisk PowerShell-arbetsflödeskörningsbok kan du välja **menyalternativet In- och utdata** och ange utdatatypen. Vi rekommenderar att du använder det fullständiga .NET-klassnamnet för att göra typen lätt identifierbar när en överordnad runbook refererar till den. Om du använder det fullständiga namnet exponeras alla egenskaper för klassen för databussen i runbooken och flexibiliteten ökar när egenskaperna används för villkorsstyrd logik, loggning och refererande som värden för andra runbook-aktiviteter.<br> ![Alternativet Runbook Input and Output](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>När du har angett ett värde i fältet **Utdatatyp** i fönstret Egenskaper för indata och utdata måste du klicka utanför kontrollen så att den känner igen posten.

I följande exempel visas två grafiska runbooks för att demonstrera funktionen In- och utdata. Med hjälp av den modulära runbook-designmodellen har du en runbook som Authenticate Runbook-mallen som hanterar autentisering med Azure med kontot Kör som. Den andra runbooken, som normalt utför kärnlogik för att automatisera ett visst scenario, kör i det här fallet mallen Authenticate Runbook. Resultaten visas i fönstret Testutdata. Under normala omständigheter skulle du låta den här runbooken göra något mot en resurs som utnyttjar utdata från den underordnade runbooken.

Här är den grundläggande logiken i **AuthenticateTo-Azure-runbooken.**<br> ![Autentisera runbookmallexempel](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Runbook innehåller utdatatypen `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, som returnerar egenskaperna för autentiseringsprofilen.<br> ![Exempel på runbook-utdatatyp](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Även om den här runbooken är enkel, finns det ett konfigurationsobjekt att ropa ut här. Den sista aktiviteten `Write-Output` kör cmdleten för att skriva profildata till `Inputobject` en variabel med hjälp av ett PowerShell-uttryck för parametern. Den här parametern krävs för `Write-Output`.

Den andra runbooken i det här exemplet, **test-childoutputType,** definierar helt enkelt två aktiviteter.<br> ![Exempel på körningskörning av underordnad utdatatyp](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Den första aktiviteten anropar **AuthenticateTo-Azure-runbooken.** Den andra aktiviteten `Write-Verbose` kör cmdlet med **datakällan** inställd **på Aktivitetsutdata**. **Fältsökvägen** är också inställd på **Context.Subscription.SubscriptionName**, kontextutdata från **AuthenticateTo-Azure-runbooken.**<br> ![Datakälla för skriv-verbose cmdletparameter](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Den resulterande utdata är namnet på prenumerationen.<br> ![Test-ChildOutputType Runbook Resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Meddelandeströmmar

Till skillnad från utdataströmmen kommunicerar meddelandeströmmar information till användaren. Det finns flera meddelandeströmmar för olika typer av information och Azure Automation hanterar varje ström på olika sätt.

### <a name="warning-and-error-streams"></a>Varnings- och felströmmar

Loggproblem för varning och fel strömmar som uppstår i en runbook. Azure Automation skriver dessa strömmar till jobbhistoriken när du kör en runbook. Automatisering innehåller strömmar i fönstret Testa utdata i Azure-portalen när en runbook testas. 

Som standard fortsätter en runbook att köras efter en varning eller ett fel. Du kan ange att runbooken ska pausas vid en varning eller ett fel genom att runbooken ska ange en [inställningsvariabel](#preference-variables) innan meddelandet skapas. Om du till exempel vill att runbooken ska pausas `ErrorActionPreference` på ett fel som det gör på ett undantag anger du variabeln till Stopp.

Skapa en varning eller ett felmeddelande med cmdleten [Skriv-varning](https://technet.microsoft.com/library/hh849931.aspx) eller [skrivfel.](https://technet.microsoft.com/library/hh849962.aspx) Aktiviteter kan också skriva till varnings- och felströmmarna.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Felsökningsström

Azure Automation använder meddelandeströmmen Debug för interaktiva användare. Det bör inte användas i runbooks.

### <a name="verbose-stream"></a>Utförlig ström

Den utförliga meddelandeströmmen stöder allmän information om runbook-åtgärden. Eftersom felsökningsströmmen inte är tillgänglig för en runbook bör runbooken använda utförliga meddelanden för felsökningsinformation. 

Som standard lagrar jobbhistoriken inte utförliga meddelanden från publicerade runbooks, av prestandaskäl. Om du vill lagra utförliga meddelanden använder du fliken **Konfigurera** Azure-portal med inställningen **Logga utförliga poster** för att konfigurera publicerade runbooks för att logga utförliga meddelanden. Aktivera bara det här alternativet om du behöver felsöka en runbook. I de flesta fall bör du behålla standardinställningen för att inte logga utförliga poster.

När [du testar en runbook](automation-testing-runbook.md)visas inte utförliga meddelanden även om runbooken är konfigurerad för att logga utförliga poster. Om du vill visa utförliga meddelanden när du `VerbosePreference` testar en [runbook](automation-testing-runbook.md)måste du ange variabeln till Fortsätt. Med den variabeluppsättningen visas utförliga meddelanden i fönstret Testa utdata i Azure-portalen.

Följande kod skapar ett utförligt meddelande med hjälp av [skriv-verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Förloppsposter

Du kan använda fliken **Konfigurera** i Azure-portalen för att konfigurera en runbook för att logga statusposter. Standardinställningen är att inte logga posterna för att maximera prestanda. I de flesta fall bör du behålla standardinställningen. Aktivera bara det här alternativet om du behöver felsöka en runbook. 

Om du aktiverar loggning av förloppspost skriver runbooken en post till jobbhistoriken före och efter varje aktivitet. Om du testar en runbook visas inte förloppsmeddelanden även om runbooken är konfigurerad för att logga förloppsposter.

>[!NOTE]
>Cmdlet för [skrivförlopp](https://technet.microsoft.com/library/hh849902.aspx) är inte giltig i en runbook, eftersom den här cmdleten är avsedd att användas med en interaktiv användare.

## <a name="preference-variables"></a>Inställningsvariabler

Du kan ange vissa Windows [PowerShell-inställningsvariabler](https://technet.microsoft.com/library/hh847796.aspx) i runbooks för att styra svaret på data som skickas till olika utdataströmmar. I följande tabell visas de inställningsvariabler som kan användas i runbooks, med standardvärden och giltiga värden. Ytterligare värden är tillgängliga för inställningsvariablerna när de används i Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| `WarningPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `ErrorActionPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppa<br>Fortsätt<br>SilentlyContinue |

I nästa tabell visas beteendet för de inställningsvariabelvärden som är giltiga i runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätt |Loggar meddelandet och fortsätter att köra Runbooken. |
| SilentlyContinue |Fortsätter att köra Runbooken utan att logga meddelandet. Det här värdet påverkar ignorera meddelandet. |
| Stoppa |Loggar meddelandet och gör uppehåll i körningen av Runbooken. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Hämta runbook-utdata och meddelanden

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Hämta runbook-utdata och meddelanden i Azure Portal

Du kan visa information om ett runbook-jobb i Azure-portalen med hjälp av fliken **Jobb** för runbooken. Projektsammanfattningen visar indataparametrarna och [utdataströmmen,](#output-stream)förutom allmän information om jobbet och eventuella undantag som har inträffat. Jobbhistoriken innehåller meddelanden från utdataströmmen och [varnings- och felströmmar.](#warning-and-error-streams) Den innehåller också meddelanden från [verbose ström](#verbose-stream) och [förlopp poster](#progress-records) om runbook är konfigurerad för att logga utförlig och förlopp poster.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Hämta runbook-utdata och meddelanden i Windows PowerShell

I Windows PowerShell kan du hämta utdata och meddelanden från en runbook med cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Den här cmdleten kräver ID för `Stream` jobbet och har en parameter som anropas för att ange den dataström som ska hämtas. Du kan ange värdet För den här parametern för att hämta alla strömmar för jobbet.

I följande exempel startas en exempel-Runbook och väntar sedan på att den ska slutföras. När runbooken har körts in samlar skriptet in runbookutdataströmmen från jobbet.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Hämta runbook-utdata och meddelanden i grafiska runbooks

För grafiska runbooks är extra loggning av utdata och meddelanden tillgängliga i form av spårning på aktivitetsnivå. Det finns två nivåer av spårning: Grundläggande och detaljerad. Grundläggande spårning visar start- och sluttiden för varje aktivitet i runbooken, plus information som är relaterad till eventuella aktivitetsförsök. Några exempel är antalet försök och aktivitetens starttid. Detaljerad spårning omfattar grundläggande spårningsfunktioner samt loggning av in- och utdata för varje aktivitet. 

För närvarande skriver spårning på aktivitetsnivå poster med hjälp av den utförliga strömmen. Därför måste du aktivera utförlig loggning när du aktiverar spårning. För grafiska runbooks med spårning aktiverat behöver du inte logga förloppsposter. Grundläggande spårning tjänar samma syfte och är mer informativ.

![Grafisk redigering jobbströmmar Visa](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Du kan se från bilden som aktiverar utförlig loggning och spårning för grafiska runbooks gör mycket mer information tillgänglig i vyn **Jobbströmmar** för produktion. Den här extra informationen kan vara nödvändig för felsökning av produktionsproblem med en runbook. 

Men om du inte kräver den här informationen för att spåra förloppet för en runbook för felsökning, kanske du vill hålla spårningen inaktiverad som en allmän praxis. Spårningsposterna kan vara särskilt många. Med grafisk runbookspårning kan du få två till fyra poster per aktivitet, beroende på din konfiguration av grundläggande eller detaljerad spårning.

**Så här aktiverar du spårning på aktivitetsnivå:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Process Automation** om du vill öppna listan över runbooks.
3. På sidan Runbooks väljer du en grafisk runbook i listan över runbooks.
4. Klicka på **Loggning och spårning**under **Inställningar.**
5. Klicka på **På** under **Logga utförliga poster**på sidan Loggning och spårning för att aktivera utförlig loggning.
6. Under **Spårning på aktivitetsnivå**ändrar du spårningsnivån till **Grundläggande** eller **Detaljerad**, baserat på den spårningsnivå du behöver.<br>

   ![Sidan Grafisk redigeringsloggning och spårning](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Hämta runbook-utdata och meddelanden i Microsoft Azure Monitor-loggar

Azure Automation kan skicka runbook-jobbstatus och jobbströmmar till din Log Analytics-arbetsyta. Azure Monitor stöder loggar som gör att du kan:

* Få insikt om dina Automation-jobb.
* Utlösa ett e-postmeddelande eller en avisering baserat på din runbook-jobbstatus, till exempel Misslyckades eller Pausad.
* Skriv avancerade frågor över jobbströmmar.
* Korrelera jobb över Automation-konton.
* Visualisera jobbhistoriken.

Mer information om hur du konfigurerar integrering med Azure Monitor-loggar för att samla in, korrelera och agera på jobbdata finns i [Vidarebefordra jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om körning av runbook, övervakning av runbook-jobb och annan teknisk information finns i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Information om hur du utformar och använder underordnade runbooks finns [i Underordnade runbooks i Azure Automation](automation-child-runbooks.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](/powershell/scripting/overview).
