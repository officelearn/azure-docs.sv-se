---
title: Runbook-utdata och meddelanden i Azure Automation
description: Desribes hur du skapar och hämtar utdata och fel meddelanden från Runbooks i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c129391c0830e0194c32a041853482f92340bbb9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405785"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-utdata och meddelanden i Azure Automation
De flesta Azure Automation runbooks har någon form av utdata. De här utdata kan vara ett fel meddelande till användaren eller ett komplext objekt som du tänker använda med en annan Runbook. Windows PowerShell innehåller [flera strömmar](/powershell/module/microsoft.powershell.core/about/about_redirection) för att skicka utdata från ett skript eller ett arbets flöde. Azure Automation fungerar annorlunda med var och en av dessa strömmar. Du bör följa bästa praxis för hur du använder var och en av dem när du skapar en Runbook.

Följande tabell innehåller en kort beskrivning av varje data flöde och deras beteende i Azure Portal för publicerade Runbooks och när du [testar en Runbook](automation-testing-runbook.md). Mer information om varje data ström finns i senare avsnitt.

| Stream | Beskrivning | Publicerad | Test |
|:--- |:--- |:--- |:--- |
| Utdata |Objekt som ska användas av andra runbooks. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Varning |Varningsmeddelande avsett för användaren. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter runbooken när ett felmeddelande visas som standard. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Utförlig |Meddelanden som ger allmän eller felsöknings information. |Skrivs till jobbhistoriken endast om utförlig loggning är aktiverad för runbooken. |Visas endast i fönstret för att testa utdata om $VerbosePreference har värdet Fortsätt i runbooken. |
| Pågår |Poster som genereras automatiskt före och efter varje aktivitet i runbook. Runbooken bör inte försöka skapa egna status poster eftersom de är avsedda för en interaktiv användare. |Skrivs till jobbhistoriken endast om förloppsloggning är aktiverad för runbooken. |Inte visas i rutan Testutdata. |
| Felsöka |Meddelanden avsedda för en interaktiv användare. Bör inte användas i Runbooks. |Skrivs inte till jobbhistoriken. |Skrivs inte till rutan Testutdata. |

## <a name="output-stream"></a>Utdataströmmen
Utdataströmmen är avsedd för utdata av objekt, som skapas av ett skript eller ett arbets flöde när det körs på rätt sätt. I Azure Automation används den här data strömmen främst för objekt som ska förbrukas av [överordnade Runbooks som anropar den aktuella runbooken](automation-child-runbooks.md). När du [anropar en infogad runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) från en överordnad runbook returnerar den data från utdataströmmen till överordnat. Använd bara utdataströmmen för att kommunicera allmän information tillbaka till användaren om du vet att runbooken aldrig anropas av en annan Runbook. Som bästa praxis, men bör du vanligtvis använda den [utförlig Stream](#verbose-stream) att kommunicera allmän information för användaren.

Du kan skriva data till den utgående dataströmmen med [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) eller genom att placera objektet på en egen rad i runbooken.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Utdata från en funktion
När du skriver till utdataström i en funktion som ingår i din Runbook skickas utdata tillbaka till runbooken. Om runbooken tilldelar dessa utdata till en variabel, är den inte skrivits till utdataströmmen. Skrivning till andra strömmar inifrån funktionen skriver till motsvarande ström för runbooken.

Överväg följande exempel-Runbook:

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

Utdataströmmen för runbook-jobbet skulle bli:

```output
Output inside of function
Output outside of function
```

Den utförliga dataströmmen för runbook-jobbet skulle bli:

```output
Verbose outside of function
Verbose inside of function
```

När du har publicerat runbooken och innan du startar den, måste du också aktivera utförlig loggning i Runbook-inställningarna för att få utförliga data strömmar.

### <a name="declaring-output-data-type"></a>Deklarerar utdata-datatyp
Ett arbetsflöde kan specificera datatypen i utdata med den [OutputType-attributet](https://technet.microsoft.com/library/hh847785.aspx). Det här attributet har ingen effekt under körning, men ger en indikation till runbook-författaren vid designtillfället utdata som förväntas från runbooken. När verktygs uppsättningen för Runbooks fortsätter att utvecklas ökar vikten av att deklarera utdatatyper i design tiden. Därför är det en bra idé att inkludera denna deklaration i alla Runbooks som du skapar.

Här är en lista med exempel på utdatatyper:

* System. String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Följande exempel-runbook matar ut ett strängobjekt och innehåller en förklaring av utdatatypen. Om din runbook matar ut en matris med en viss typ, bör du fortfarande ange typen till skillnad från en matris av typen.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Om du vill deklarera en utdatatyp i grafiska eller grafiska PowerShell Workflow-Runbooks, kan du välja meny alternativet **indata och utdata** och skriva in namnet på utdatatypen. Vi rekommenderar att du använder det fullständiga .NET-klass namnet för att göra det enkelt att identifiera när du refererar till den från en överordnad Runbook. Detta visar alla egenskaper för den klassen för data bussen i runbooken och ger mycket flexibilitet när du använder dem för villkorlig logik, loggning och referenser som värden för andra aktiviteter i runbooken.<br> ![Alternativ för indata och utdata för Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

I följande exempel har du två grafiska runbooks som demonstrerar den här funktionen. Om du använder design modellen modulär Runbook, har du en Runbook, som fungerar som Runbook- *mallen för autentisering* hantera autentisering med Azure med hjälp av kör som-kontot. Vår andra Runbook, som normalt skulle utföra huvud logiken för att automatisera ett specifikt scenario, kommer i det här fallet att köra *Runbook-mallen för autentisering* och visa resultatet i fönstret för att **testa** utdata. Under normala omständigheter skulle du ha denna Runbook att göra något mot en resurs som utnyttjar utdata från den underordnade runbooken.

Här är den grundläggande logiken i **AuthenticateTo-Azure-** runbooken.<br> ![Exempel](media/automation-runbook-output-and-messages/runbook-authentication-template.png)på autentisering av Runbook-mall.  

Den innehåller utdatatypen *Microsoft. Azure. commands. Profile. Models. PSAzureContext*, som returnerar egenskaperna för verifierings profilen.<br> ![Exempel på Utdatatyp för Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Det här Runbook-flödet är rakt framåt, men det finns ett konfigurations objekt som kan anropas här. Den senaste aktiviteten kör cmdleten **Write-output** och skriver profil data till en $ _-variabel med ett PowerShell-uttryck för parametern **InputObject** , vilket krävs för denna cmdlet.  

För den andra runbooken i det här exemplet har namnet *test-ChildOutputType*, så du har bara två aktiviteter.<br> ![Exempel på underordnad Utdatatyp Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Den första aktiviteten anropar **AuthenticateTo-Azure-** runbooken och den andra aktiviteten kör cmdleten **Write-Verbose** med **data källan** för **aktivitets utdata** och värdet för **fält Sök väg** är  **Context. Subscription. SubscriptionName**, som anger kontextens utdata från **AuthenticateTo-Azure-** runbooken.<br> ![Data källa för Write-utförlig cmdlet-parameter](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Resultatet är namnet på prenumerationen.<br> ![Test-ChildOutputType Runbook-resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

En kommentar om beteendet för kontrollen av Utdatatyp. När du skriver ett värde i fältet Utdatatyp på bladet för in-och utdata-egenskaper måste du klicka utanför kontrollen när du har skrivit det för att din post ska kunna identifieras av kontrollen.  

## <a name="message-streams"></a>Meddelandeströmmar
Till skillnad från utdataströmmen är meddelandeströmmar avsedda att förmedla information till användaren. Det finns flera meddelande strömmar för olika typer av information, och varje hanteras på ett annat sätt med Azure Automation.

### <a name="warning-and-error-streams"></a>Varnings-och fel strömmar
Varningar och felströmmar är avsedda att logga problem som uppstår i en runbook. De skrivs till jobb historiken när en runbook körs och ingår i fönstret Testa utdata i Azure Portal när en Runbook testas. Som standard fortsätter runbook att köras efter en varning eller fel. Du kan ange att runbook ska göra uppehåll när en varning eller fel genom att ange en [inställningsvariabeln](#preference-variables) i runbooken innan du skapar meddelandet. Om du till exempel vill göra en Runbook inaktive rad vid ett fel som ett undantag, ställer du in **$ErrorActionPreference** att stoppa.

Skapa en varning eller fel meddelande med hjälp av den [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) eller [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet. Aktiviteter kan också skriva till dessa strömmar.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Utförlig dataström
Den utförliga meddelandeströmmen är allmän information om runbook-åtgärden. Eftersom [fel söknings strömmen](#debug-stream) inte är tillgänglig i en Runbook ska utförliga meddelanden användas för felsöknings information. Som standard lagras utförliga meddelanden från publicerade Runbooks inte i jobb historiken. Om du vill lagra utförliga meddelanden konfigurerar du publicerade Runbooks att logga utförliga poster på fliken Konfigurera i Runbook i Azure Portal. I de flesta fall bör du behålla standardinställningen att inte logga utförliga poster för en runbook av prestandaskäl. Aktivera det här alternativet endast att felsöka en runbook.

När du [testar en Runbook](automation-testing-runbook.md)visas inte utförliga meddelanden även om runbooken har kon figurer ATS för att logga utförliga poster. Om du vill visa utförliga meddelanden när du [testar en Runbook](automation-testing-runbook.md)måste du ange variabeln $VerbosePreference för att fortsätta. Med den variabel uppsättningen visas utförliga meddelanden i fönstret Testa utdata i Azure Portal.

Skapa ett utförligt meddelande med den [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Felsökningsströmmen
Felsökningsströmmen är avsedd att användas med en interaktiv användare och ska inte användas i runbooks.

## <a name="progress-records"></a>Förloppsposter
Om du konfigurerar en Runbook att logga status poster (på fliken Konfigurera i runbooken i Azure Portal), skrivs en post till jobb historiken före och efter att varje aktivitet körs. I de flesta fall bör du behålla standardinställningen att inte logga förloppsposter för en runbook för att maximera prestanda. Aktivera det här alternativet endast att felsöka en runbook. När du testar en runbook, visas inte förloppsmeddelanden även om runbooken har konfigurerats för att logga förloppsposter.

Cmdleten [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) är inte giltig i en Runbook eftersom denna cmdlet är avsedd att användas med en interaktiv användare.

## <a name="preference-variables"></a>Preferensvariabler
Windows PowerShell använder [preferensvariabler](https://technet.microsoft.com/library/hh847796.aspx) att avgöra hur du svarar på data som skickas till olika utdataströmmar. Du kan ställa in dessa variabler i en Runbook för att styra hur den svarar på data som skickas till olika strömmar.

I följande tabell visas de preferensvariabler som kan användas i runbooks med sina giltiga och standardvärden. Tabellen innehåller endast de värden som är giltiga i en Runbook. Ytterligare värden är giltiga för Preferences-variablerna när de används i Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| WarningPreference |Fortsätta |Stop<br>Fortsätta<br>SilentlyContinue |
| ErrorActionPreference |Fortsätta |Stop<br>Fortsätta<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Fortsätta<br>SilentlyContinue |

I följande tabell visas beteendet för de preferensvariabelvärden som är giltiga i runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätta |Loggar meddelandet och fortsätter att köra runbooken. |
| SilentlyContinue |Fortsätter att köra runbooken utan att logga meddelandet. Det här värdet innebär att meddelandet ignoreras. |
| Stoppa |Loggar meddelandet och pausar runbooken. |

## <a name="runbook-output"></a>Hämta utdata och meddelanden i Runbook
### <a name="azure-portal"></a>Azure Portal
Du kan visa information om ett Runbook-jobb i Azure Portal från fliken jobb i en Runbook. Sammanfattningen av jobbet visar indataparametrarna och [utdataströmmen](#output-stream) Förutom allmän information om jobbet och eventuella undantag om de har inträffat. Historiken innehåller meddelanden från [utdataströmmen](#output-stream) och varnings- [och fel strömmar](#warning-and-error-streams) samt [utförlig data ström](#verbose-stream) och [förlopps poster](#progress-records) om runbooken är konfigurerad att logga utförliga och förlopps poster.

### <a name="windows-powershell"></a>Windows PowerShell
I Windows PowerShell kan du hämta utdata och meddelanden från en Runbook med hjälp av cmdleten [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . Denna cmdlet kräver jobbets ID och har en parameter med namnet Stream där du anger vilken data ström som ska returneras. Du kan ange **alla** att returnera alla dataströmmar för jobbet.

I följande exempel startas en exempel-runbook och väntar sedan tills den är klar. När klar utdataström dess från jobbet.

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

### <a name="graphical-authoring"></a>Grafisk redigering
För grafiska runbooks är extra loggning tillgänglig i form av spårning på aktivitets nivå. Det finns två nivåer av spårning: Basic och detaljerad. I grundläggande spårning kan du se start-och slut tid för varje aktivitet i Runbook plus information som rör eventuella aktivitets försök. Några exempel är, antalet försök och start tid för aktiviteten. I detaljerad spårning får du grundläggande spårning plus indata och utdata för varje aktivitet. Spårnings posterna skrivs för närvarande med hjälp av utförlig data ström, så du måste aktivera utförlig loggning när du aktiverar spårning. För grafiska runbooks med spårning aktive rad behöver du inte logga förlopps poster. Grundläggande spårning har samma syfte och är mer informativt.

![Grafiskt redigerings jobb för jobb visning](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Du kan se från föregående skärm bild som när du aktiverar utförlig loggning och spårning för grafiska runbooks. mycket mer information finns i vyn jobb strömmar för produktion. Den här extra informationen kan vara avgörande för fel sökning av produktions problem med en Runbook, och därför bör du bara aktivera den för detta ändamål och inte som en allmän praxis. Spårnings posterna kan vara särskilt många. Med grafisk Runbook-spårning kan du hämta två till fyra poster per aktivitet beroende på om du har konfigurerat Basic eller detaljerad spårning. Om du inte behöver den här informationen för att följa förloppet för en Runbook för fel sökning kan du fortsätta att spåra inaktive rad.

**Utför följande steg för att aktivera spårning på aktivitets nivå:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Under **process automatisering**väljer du **Runbooks** för att öppna listan över Runbooks.
3. På sidan Runbooks klickar du på för att välja en grafisk Runbook från listan över Runbooks.
4. Under **Inställningar**klickar du på **loggning och spårning**.
5. På sidan loggning och spårning, under Logga utförliga poster, klickar du på **på** för att aktivera utförlig loggning och under spårning på aktivitets nivå ändrar du spårnings nivån till **grundläggande** eller **detaljerad** baserat på den spårnings nivå som du behöver.<br>
   
   ![Grafisk redigering av loggnings-och spårnings sida](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure övervaka loggar
Automation kan skicka status för Runbook-jobb och jobb strömmar till din Log Analytics-arbetsyta. Med Azure Monitor loggar kan du

* Få insikt om dina automatiserings jobb 
* Utlös ett e-postmeddelande eller en avisering baserat på status för Runbook-jobb (till exempel misslyckad eller inaktive rad) 
* Skriv avancerade frågor i dina jobb strömmar 
* Korrelera jobb över Automation-konton 
* Visualisera jobb historiken över tid    

Mer information om hur du konfigurerar integrering med Azure Monitor loggar för att samla in, korrelera och agera på jobb data finns i [vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
* Information om hur du utformar och använder underordnade Runbooks finns under [underordnade Runbooks i Azure Automation](automation-child-runbooks.md)


