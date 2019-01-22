---
title: Runbook-utdata och meddelanden i Azure Automation
description: Beskriver hur du skapar och hämta utdata och meddelanden från runbooks i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82382ecc3adf0d0621f51438a082f7807b031fc9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431222"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-utdata och meddelanden i Azure Automation
De flesta Azure Automation-runbooks har någon form av utdata. Dessa utdata kan ett felmeddelande till användaren eller ett komplext objekt som du planerar att använda med en annan runbook. Windows PowerShell innehåller [flera strömmar](/powershell/module/microsoft.powershell.core/about/about_redirection) att skicka utdata från ett skript eller ett arbetsflöde. Azure Automation fungerar på olika sätt med var och en av dessa strömmar. Du bör följa bästa praxis för hur du använder när du skapar en runbook.

Följande tabell innehåller en kort beskrivning av varje ström och deras beteende i Azure-portalen för publicerade runbooks och när [testar en runbook](automation-testing-runbook.md). Mer information om varje dataström finns i senare avsnitt.

| Stream | Beskrivning | Publicerad | Test |
|:--- |:--- |:--- |:--- |
| Utdata |Objekt som ska användas av andra runbooks. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Varning |Varningsmeddelande avsett för användaren. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter runbooken när ett felmeddelande visas som standard. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Utförlig |Meddelanden som ger information om allmänna eller felsökning. |Skrivs till jobbhistoriken endast om utförlig loggning är aktiverad för runbooken. |Visas i rutan Testutdata endast om $VerbosePreference är inställt på Fortsätt i runbooken. |
| Förlopp |Poster som genereras automatiskt före och efter varje aktivitet i runbook. Runbook bör inte försöka skapa sina egna förloppsposter eftersom de är avsedda för en interaktiv användare. |Skrivs till jobbhistoriken endast om förloppsloggning är aktiverad för runbooken. |Inte visas i rutan Testutdata. |
| Felsöka |Meddelanden avsedda för en interaktiv användare. Bör inte användas i runbooks. |Skrivs inte till jobbhistoriken. |Skrivs inte till rutan Testutdata. |

## <a name="output-stream"></a>Utdataströmmen
Utdataströmmen är avsedd för utdata för objekt som skapas av ett skript eller ett arbetsflöde när det körs korrekt. I Azure Automation används främst dataströmmen för objekt som ska användas av [överordnade runbooks som anropar den aktuella runbooken](automation-child-runbooks.md). När du [anropar en infogad runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) från en överordnad runbook returnerar den data från utdataströmmen till överordnat. Använd bara utdataströmmen för att kommunicera allmän information tillbaka till användaren om du vet att runbooken aldrig anropas av andra runbooks. Som bästa praxis, men bör du vanligtvis använda den [utförlig Stream](#verbose-stream) att kommunicera allmän information för användaren.

Du kan skriva data till den utgående dataströmmen med [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) eller genom att placera objektet på en egen rad i runbooken.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Utdata från en funktion
När du skriver till utdataströmmen i en funktion som ingår i din runbook skickas utdata tillbaka till runbook. Om runbooken tilldelar dessa utdata till en variabel, är den inte skrivits till utdataströmmen. Skrivning till andra dataströmmar inifrån funktionen skriver till motsvarande dataström för runbook.

Överväg följande exempel-runbook:

```PowerShell
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

När du har publicerat en runbook och innan du börjar den, måste du också aktivera utförlig loggning i runbook-inställningar för att få utförlig dataström-utdata.

### <a name="declaring-output-data-type"></a>Deklarerande Utdatatyp
Ett arbetsflöde kan specificera datatypen i utdata med den [OutputType-attributet](https://technet.microsoft.com/library/hh847785.aspx). Det här attributet har ingen effekt under körning, men ger en indikation till runbook-författaren vid designtillfället utdata som förväntas från runbooken. När verktygsuppsättningen för runbooks fortsätter att utvecklas ökar vikten av att deklarera utdatatyper vid designtillfället betydelse. Därför är det en bra idé att inkludera deklarationen i alla runbooks som du skapar.

Här är en lista med exempel utdatatyper:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Följande exempel-runbook matar ut ett strängobjekt och innehåller en förklaring av utdatatypen. Om din runbook matar ut en matris med en viss typ, bör du fortfarande ange typen till skillnad från en matris av typen.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

För att deklarera en Utdatatyp i grafisk eller grafiskt PowerShell-arbetsflöde runbooks, kan du välja den **indata och utdata** menyalternativet och Skriv namnet på utdatatypen. Det rekommenderas att du använder det fullständiga namnet för .NET-klass för att göra det lätt att identifiera när du refererar till den från en överordnad runbook. Detta visar alla egenskaper av den klassen i databussen i runbook och ger mycket flexibilitet när du använder dem för att införa villkorslogik, loggning och referera till som värden för andra aktiviteter i runbook.<br> ![Indata för Runbook-och utdata](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

I följande exempel har du två grafiska runbooks för att demonstrera den här funktionen. Om du använder modellen modulära runbook design, du har en runbook, som fungerar som den *mall för autentisering Runbook* hantera autentisering med Azure med hjälp av kör som-kontot. Våra andra runbook, som normalt skulle utföra kärnlogik för att automatisera ett visst scenario i det här fallet kommer att köra den *mall för autentisering Runbook* och visa resultaten till din **Test** utdatafönstret. Under normala omständigheter behöver denna runbook göra något mot en resurs att använda utdata från den underordnade runbooken.

Här är den grundläggande logiken i den **AuthenticateTo Azure** runbook.<br> ![Autentisera mall för Runbook-exempel](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Den innehåller utdatatypen *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, vilket returnerar autentiseringen profilegenskaper.<br> ![Exempel på utdata för Runbook-typ](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Denna runbook är enkelt, finns men det ett objekt som ska ringa in här. Den senaste aktiviteten körs den **Write-Output** cmdlet och skriver data för användarprofiler $_ variabler med hjälp av ett PowerShell-uttryck för den **Inputobject** parametern, som krävs för cmdleten.  

Andra runbooken i det här exemplet heter *Test-ChildOutputType*, du behöver bara två aktiviteter.<br> ![Exempel underordnade utgående typ av Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

De första aktiviteten-anrop i **AuthenticateTo Azure** runbook och den andra aktiviteten körs den **Write-Verbose** cmdlet med den **datakälla** av  **Aktivitetsutdata** och värdet för **fältsökväg** är **Context.Subscription.SubscriptionName**, vilket är anger kontexten utdata från den  **Azure-AuthenticateTo** runbook.<br> ![Cmdleten Write-Verbose parametern datakälla](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Resultatet är namnet på prenumerationen.<br> ![Test-ChildOutputType Runbook-resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

En anteckning om beteendet i kontrollen utdatatypen. Du måste klicka på utanför kontrollen när du har skrivit det, för att ditt bidrag ska kunna identifieras av kontrollen när du skriver ett värde i fältet Typ av utdata i egenskapsbladet för indata och utdata.  

## <a name="message-streams"></a>Meddelandeströmmar
Till skillnad från utdataströmmen är meddelandeströmmar avsedda att förmedla information till användaren. Det finns flera meddelandeströmmar för olika typer av information och var och en hanteras olika av Azure Automation.

### <a name="warning-and-error-streams"></a>Varningar och felströmmar
Varningar och felströmmar är avsedda att logga problem som uppstår i en runbook. De skrivs till jobbhistoriken när en runbook körs och ingår i rutan Testutdata i Azure-portalen när en runbook testas. Som standard fortsätter runbook att köras efter en varning eller fel. Du kan ange att runbook ska göra uppehåll när en varning eller fel genom att ange en [inställningsvariabeln](#preference-variables) i runbooken innan du skapar meddelandet. Till exempel för att utlösa en runbook att göra uppehåll när ett fel när ett undantag, ange **$ErrorActionPreference** att stoppa.

Skapa en varning eller fel meddelande med hjälp av den [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) eller [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet. Aktiviteter kan också skriva till dessa strömmar.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Utförlig dataström
Den utförliga meddelandeströmmen är allmän information om runbook-åtgärden. Eftersom den [Debug Stream](#debug-stream) är inte tillgänglig i en runbook utförliga meddelanden ska användas för felsökningsinformation. Som standard lagras inte utförliga meddelanden från publicerade runbooks i jobbhistoriken. För att lagra utförliga meddelanden konfigurerar du publicerade runbooks till utförlig loggning på fliken Konfigurera för runbook i Azure-portalen. I de flesta fall bör du behålla standardinställningen att inte logga utförliga poster för en runbook av prestandaskäl. Aktivera det här alternativet endast att felsöka en runbook.

När [testar en runbook](automation-testing-runbook.md), utförliga meddelanden visas inte även om runbooken har konfigurerats för att logga utförliga poster. Att visa utförliga meddelanden vid [testar en runbook](automation-testing-runbook.md), måste du ange variabeln $VerbosePreference till Fortsätt. Med dessa variabeln visas utförliga meddelanden i rutan Testutdata i Azure Portal.

Skapa ett utförligt meddelande med den [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Felsökningsströmmen
Felsökningsströmmen är avsedd att användas med en interaktiv användare och ska inte användas i runbooks.

## <a name="progress-records"></a>Förloppsposter
Om du konfigurerar en runbook för att logga förloppet registrerar (på fliken Konfigurera för runbook i Azure portal) och en post skrivs till jobbhistoriken före och efter varje aktivitet körs. I de flesta fall bör du behålla standardinställningen att inte logga förloppsposter för en runbook för att maximera prestanda. Aktivera det här alternativet endast att felsöka en runbook. När du testar en runbook, visas inte förloppsmeddelanden även om runbooken har konfigurerats för att logga förloppsposter.

Den [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet är inte giltig i en runbook, eftersom denna cmdlet är avsedd att användas med en interaktiv användare.

## <a name="preference-variables"></a>Preferensvariabler
Windows PowerShell använder [preferensvariabler](https://technet.microsoft.com/library/hh847796.aspx) att avgöra hur du svarar på data som skickas till olika utdataströmmar. Du kan ange dessa variabler i en runbook och styra hur den svarar på data som skickas till olika dataströmmar.

I följande tabell visas de preferensvariabler som kan användas i runbooks med sina giltiga och standardvärden. Den här tabellen bara innehåller de värden som är giltiga i en runbook. Ytterligare värden är giltiga för preferensvariabler när de används i Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| WarningPreference |Fortsätta |Stop<br>Fortsätta<br>SilentlyContinue |
| ErrorActionPreference |Fortsätta |Stop<br>Fortsätta<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Fortsätta<br>SilentlyContinue |

I följande tabell visas beteendet för de preferensvariabelvärden som är giltiga i runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätta |Loggar meddelandet och fortsätter att köra runbooken. |
| SilentlyContinue |Fortsätter att köra runbooken utan att logga meddelandet. Det här värdet har ignoreras meddelandet. |
| Stoppa |Loggar meddelandet och pausar runbooken. |

## <a name="runbook-output"></a>Hämtning av runbook-utdata och meddelanden
### <a name="azure-portal"></a>Azure Portal
Du kan visa information om ett runbook-jobb i Azure-portalen från fliken jobb i en runbook. Sammanfattning av jobbet visar indataparametrarna och [utdata Stream](#output-stream) samt allmän information om jobbet och eventuella undantag uppstår. Historiken innehåller meddelanden från den [utdata Stream](#output-stream) och [varningar och Felströmmar](#warning-and-error-streams) samt de [utförlig Stream](#verbose-stream) och [Förloppsposter](#progress-records) om runbooken har konfigurerats att logga utförliga poster och förloppsposter.

### <a name="windows-powershell"></a>Windows PowerShell
I Windows PowerShell kan du hämta utdata och meddelanden från en runbook med hjälp av den [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) cmdlet. Denna cmdlet kräver ID för jobbet och har en parameter anropas Stream anger du vilken dataström som ska returneras. Du kan ange **alla** att returnera alla dataströmmar för jobbet.

I följande exempel startas en exempel-runbook och väntar sedan tills den är klar. När klar utdataström dess från jobbet.

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Grafisk redigering
För grafiska runbooks är extra loggning tillgänglig i form av aktivitet på servernivå spårning. Det finns två nivåer av spårning: Grundläggande och detaljerad. I grundläggande spårning ser du start- och tidpunkten för varje aktivitet i runbook samt information som rör alla återförsök för aktiviteten. Några exempel är antalet försök och starttid för aktiviteten. I Detaljerad spårning får du grundläggande spårning plus indata och utdata för varje aktivitet. För närvarande skrivs trace-poster med hjälp av den utförliga dataströmmen, så måste du aktivera utförlig loggning när du aktiverar spårning. För grafiska runbooks med aktiverad spårning finns inget behov av att logga förloppsposter. Grundläggande spårning har samma funktion och är mer informativ.

![Grafisk redigering jobbet strömmar vy](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Från föregående skärmbild kan du se att när du aktiverar utförlig loggning och spårning för grafiska runbooks kan mycket mer information finns i produktion Jobbströmmar vy. Den här extra information kan vara nödvändigt för felsökning av produktionsproblem i med en runbook och därför bör du bara aktivera det för detta ändamål och inte som en allmän regel. Spåra poster kan vara särskilt många. Med en grafisk runbook spårning får du två till fyra poster per aktivitet beroende på om du har konfigurerat grundläggande eller detaljerat spårning. Om du inte behöver denna information för att följa förloppet för en runbook för felsökning, kan du hålla spårning stängs av.

**Om du vill aktivera aktivitetsnivån spårning, utför du följande steg:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Under **Processautomatisering**väljer **Runbooks** att öppna listan med runbooks.
3. På sidan Runbooks för att välja en grafisk runbook från listan med runbooks.
4. Under **inställningar**, klickar du på **loggning och spårning**.
5. Klicka på loggning och spårning av sidan under utförlig loggning **på** att aktivera utförlig loggning och ändra spårningsnivån till under aktivitetsnivån spårning, **grundläggande** eller **detaljerat** baserat på nivån för spårning som du behöver.<br>
   
   ![Grafisk redigering loggning och spårning av sidan](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics
Automation kan skicka runbook jobbet status och jobbströmmar till Log Analytics-arbetsytan. Med Log Analytics kan du,

* Skaffa dig insikter om dina Automation-jobb 
* Utlösaren som en e-post eller en avisering baserat på din runbook jobbstatus (till exempel växlas eller har pausats) 
* Skriva avancerade frågor över din jobbströmmar 
* Kombinera jobb i Automation-konton 
* Visualisera jobbets historik över tid    

Läs mer om hur du konfigurerar du integrering med Log Analytics för att samla in, korrelera och agera på jobbdata [vidarebefordrar jobbstatus och jobbströmmar från Automation till Log Analytics](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
* Information om hur du utformar och använda underordnade runbooks finns i [underordnade runbooks i Azure Automation](automation-child-runbooks.md)


