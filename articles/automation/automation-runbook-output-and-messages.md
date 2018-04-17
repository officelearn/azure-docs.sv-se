---
title: Runbook-utdata och meddelanden i Azure Automation
description: Beskriver hur du skapar och hämta utdata och fel meddelanden från runbooks i Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d4931c710bebc5e6c3ee23fb58e1432bb86da4a5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-utdata och meddelanden i Azure Automation
De flesta Azure Automation-runbooks har någon form av utdata, till exempel ett felmeddelande för användaren eller ett komplext objekt som är avsedd att användas av ett annat arbetsflöde. Windows PowerShell innehåller [flera strömmar](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) att skicka utdata från ett skript eller ett arbetsflöde. Azure Automation fungerar olika med var och en av dessa strömmar och du bör följa bästa praxis för hur du använder när du skapar en runbook.

Följande tabell innehåller en kort beskrivning av varje ström och deras beteende i Azure-portalen, både när du kör en publicerad runbook och när [testar en runbook](automation-testing-runbook.md). Mer information om varje dataström finns i följande avsnitt.

| Stream | Beskrivning | Publicerad | Testa |
|:--- |:--- |:--- |:--- |
| Resultat |Objekt som ska användas av andra runbooks. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Varning |Varningsmeddelande avsett för användaren. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter runbooken när ett felmeddelande visas som standard. |Skrivs till jobbhistoriken. |Visas i rutan Testutdata. |
| Utförlig |Meddelanden som ger information om allmänna eller felsökning. |Skrivs till jobbhistoriken endast om utförlig loggning är aktiverad för runbooken. |Visas i rutan Testutdata om $VerbosePreference är inställt på Fortsätt i runbook. |
| Förlopp |Poster som genereras automatiskt före och efter varje aktivitet i runbook. Runbook bör inte försöka skapa sina egna förloppsposter eftersom de är avsedda för en interaktiv användare. |Skrivs till jobbhistoriken endast om förloppsloggning är aktiverad för runbooken. |Inte visas i rutan Testutdata. |
| Felsökning |Meddelanden avsedda för en interaktiv användare. Bör inte användas i runbooks. |Skrivs inte till jobbhistoriken. |Skrivs inte till rutan Testutdata. |

## <a name="output-stream"></a>Utdataström
Utdataströmmen är avsedd för utdata från objekt som skapats av ett skript eller ett arbetsflöde när det körs korrekt. I Azure Automation den här strömmen framför allt för objekt som ska konsumeras av [överordnade runbooks som anropar den aktuella runbooken](automation-child-runbooks.md). När du [anropar en infogad runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) från en överordnad runbook returnerar den data från utdataströmmen till överordnat. Du bör endast använda utdataströmmen för att kommunicera allmän information tillbaka till användaren om du vet att runbooken aldrig anropas av en annan runbook. Som bästa praxis bör du vanligtvis använder, den [utförliga strömmen](#Verbose) att kommunicera allmän information till användaren.

Du kan skriva till dataströmmen utdata med [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) eller genom att placera objektet på en egen rad i runbooken.

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

Utdataströmmen för runbook-jobbet är:

    Output inside of function
    Output outside of function

Den utförliga dataströmmen för runbook-jobbet är:

    Verbose outside of function
    Verbose inside of function

När du har publicerat runbook och innan du börjar måste du också aktivera utförlig loggning i runbook-inställningar för att kunna hämta utdata utförliga strömmen.

### <a name="declaring-output-data-type"></a>Deklarerande Utdatatyp
Ett arbetsflöde kan specificera datatypen i utdata med den [OutputType-attributet](http://technet.microsoft.com/library/hh847785.aspx). Det här attributet har ingen effekt under körning, men ger en indikation till runbook-författaren vid designtillfället vilka utdata som förväntas av runbook. När verktygsuppsättningen för runbooks fortsätter att utvecklas ökar vikten av att deklarera utdatatyper i designläge betydelse. Därför är det en bra idé att inkludera deklarationen i alla runbooks som du skapar.

Här är en lista över exempel utdata typer:

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

För att deklarera Utdatatyp i grafisk eller grafisk PowerShell-arbetsflöde runbooks, kan du välja den **indata och utdata** menyalternativet och Skriv namnet på utdatatypen. Det rekommenderas att du använder det fullständiga namnet för .NET-klass för att göra det lätt att identifiera när du refererar till den från en överordnad runbook. Detta visar alla egenskaper av den klassen i databussen i runbook och ger mycket flexibilitet när du använder dem för att införa villkorslogik, loggning och refererar till som värden för andra aktiviteter i runbook.<br> ![Alternativet Runbook indata och utdata](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

I följande exempel har två grafiska runbook för att visa den här funktionen. Om du tillämpar modulära runbook designmodellen har du en runbook som fungerar som den *mall för autentisering Runbook* hantera autentisering med Azure med hjälp av kör som-kontot. Våra andra runbook, som normalt skulle utföra grundläggande logik för att automatisera ett visst scenario i det här fallet ska köra den *mall för autentisering Runbook* och visa resultatet till din **Test** utdatafönstret. Under normala omständigheter måste denna runbook gör något mot en resurs som utnyttjar utdata från den underordnade runbooken.    

Här är den grundläggande logiken i den **AuthenticateTo Azure** runbook.<br> ![Autentisera mall för Runbook-exempel](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Den omfattar utdatatypen *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, som returnerar autentiseringen profilegenskaper.<br> ![Exempel på utdata för Runbook typ](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Denna runbook är direkt framåt, finns men det ett konfigurationsobjekt för att anropa här. Den senaste aktiviteten körs den **Write-Output** cmdlet och skriver data för användarprofiler till en $_ variabel med hjälp av en PowerShell-uttryck för den **Inputobject** som krävs för denna cmdlet.  

För andra runbook i det här exemplet *Test ChildOutputType*, du bara har två aktiviteter.<br> ![Exempel underordnade utdata typen Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

De första aktiviteten anrop i **AuthenticateTo Azure** runbook och den andra aktiviteten körs den **Write-Verbose** med den **datakällan** av  **Aktivitetsutdata** och värdet för **fältet sökväg** är **Context.Subscription.SubscriptionName**, vilket är att ange kontexten utdata från den  **AuthenticateTo Azure** runbook.<br> ![Write-Verbose cmdlet parametern datakälla](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Resultatet är namnet på prenumerationen.<br> ![Testa ChildOutputType Runbook-resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

En anteckning om beteendet för utdatatypen kontrollen. När du skriver ett värde i fältet utdatatypen i egenskapsbladet ingående och utgående måste du klicka utanför kontrollen när du skriver in, för att posten ska kunna identifieras av kontrollen.  

## <a name="message-streams"></a>Meddelandeströmmar
Till skillnad från utdataströmmen är avsedda meddelandeströmmar att skicka information till användaren. Det finns flera meddelandeströmmar för olika typer av information och var och en hanteras olika av Azure Automation.

### <a name="warning-and-error-streams"></a>Varningar och felströmmar
Varningar och felströmmar är avsedda att logga problem som uppstår i en runbook. De skrivs till jobbhistoriken när en runbook körs och ingår i rutan Testutdata i Azure-portalen när en runbook testas. Som standard fortsätter runbook att köras efter en varning eller fel. Du kan ange att runbook ska göra uppehåll när en varning eller fel genom att ange en [inställningsvariabeln](#PreferenceVariables) i runbooken innan du skapar meddelandet. Till exempel om du vill att en runbook att göra uppehåll när ett fel som ett undantag, ange **$ErrorActionPreference** till Stopp.

Skapa en varning eller fel meddelande med hjälp av den [Write-Warningg](https://technet.microsoft.com/library/hh849931.aspx) eller [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) cmdlet. Aktiviteter kan också skriva till dessa strömmar.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Utförlig dataström
Den utförliga meddelandeströmmen är för allmän information om runbook-åtgärden. Eftersom den [Felsökningsströmmen](#Debug) är inte tillgänglig i en runbook utförliga meddelanden ska användas för felsökningsinformation. Som standard lagras inte utförliga meddelanden från publicerade runbooks i jobbhistoriken. Om du vill lagra utförliga meddelanden konfigurerar du publicerade runbooks till logga utförliga meddelanden på fliken Konfigurera runbook i Azure-portalen. I de flesta fall bör du behålla standardinställningen att inte logga utförliga poster för en runbook av prestandaskäl. Aktivera det här alternativet endast att felsöka en runbook.

När [testar en runbook](automation-testing-runbook.md), visas inte utförlig meddelanden även om runbooken har konfigurerats för att logga utförliga poster. Visa utförliga meddelanden vid [testar en runbook](automation-testing-runbook.md), måste du ange variabeln $VerbosePreference till Fortsätt. Med dessa variabeln visas utförliga meddelanden i rutan Testutdata i Azure-portalen.

Skapa ett utförligt meddelande med den [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Felsökningsströmmen
Felsökningsströmmen är avsedd att användas med en interaktiv användare och ska inte användas i runbooks.

## <a name="progress-records"></a>Förloppsposter
Om du konfigurerar en runbook för att logga förlopp registrerar (på fliken Konfigurera runbook i Azure portal) och sedan en post skrivs till jobbhistoriken före och efter varje aktivitet körs. I de flesta fall bör du behålla standardinställningen att inte logga förloppsposter för en runbook för att maximera prestandan. Aktivera det här alternativet endast att felsöka en runbook. När du testar en runbook visas inte förloppsmeddelanden även om runbooken har konfigurerats för att logga förloppsposter.

Den [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) cmdlet är inte giltig i en runbook eftersom den är avsedd för användning med en interaktiv användare.

## <a name="preference-variables"></a>Preferensvariabler
Windows PowerShell använder [preferensvariabler](http://technet.microsoft.com/library/hh847796.aspx) att bestämma hur du ska svara på data som skickas till olika utdataströmmar. Du kan ange dessa variabler i en runbook och styra hur den ska svara på data som skickas till olika dataströmmar.

I följande tabell visas de preferensvariabler som kan användas i runbooks med sina giltiga värden och standardvärden. Den här tabellen bara innehåller de värden som är giltiga i en runbook. Ytterligare värden är giltiga för preferensvariabler när de används i Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| WarningPreference |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| ErrorActionPreference |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stoppa<br>Fortsätt<br>SilentlyContinue |

I följande tabell visas beteendet för de preferensvariabelvärden som är giltiga i runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätt |Loggar meddelandet och fortsätter att köra runbooken. |
| SilentlyContinue |Fortsätter att köra runbooken utan att logga meddelandet. Detta påverkar att meddelandet ignoreras. |
| Stoppa |Loggar meddelandet och pausar runbooken. |

## <a name="retrieving-runbook-output-and-messages"></a>Hämtning av runbook-utdata och meddelanden
### <a name="azure-portal"></a>Azure Portal
Du kan visa information om ett runbook-jobb på Azure-portalen på fliken jobb i en runbook. Sammanfattning av jobbet visar indataparametrarna och [utdataströmmen](#Output) förutom allmän information om jobbet och eventuella undantag de inträffade. Historiken innehåller meddelanden från den [utdataströmmen](#Output) och [varningar och Felströmmar](#WarningError) förutom den [utförliga strömmen](#Verbose) och [Förloppsposter](#Progress) om runbooken har konfigurerats att logga utförliga poster och förloppsposter.

### <a name="windows-powershell"></a>Windows PowerShell
I Windows PowerShell kan du hämta utdata och meddelanden från en runbook med den [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) cmdlet. Denna cmdlet kräver ID för jobbet och har en parameter som kallas dataströmmen där du kan ange vilken dataström som ska returneras. Du kan ange **alla** att returnera alla dataströmmar för jobbet.

Följande exempel startas en exempel-runbook och väntar sedan tills den är klar. När slutfört, utdataström dess från jobbet.

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
Grafiska runbook-flöden är extra loggning tillgängligt i form av aktivitetsnivå spårning. Det finns två nivåer av spårning: Basic och detaljerad. I grundläggande spårning visas i början och sluttid för varje aktivitet i runbook samt information som rör någon aktivitet omförsök, till exempel antal försök och starttid på aktiviteten. I Detaljerad spårning får du grundläggande spårning plus indata och utdata för varje aktivitet. För närvarande skrivs trace-poster med den utförliga strömmen så du måste aktivera utförlig loggning när du aktiverar spårning. För grafiska runbook-flöden och spårning har aktiverats, finns inget behov av att logga förloppsposter, eftersom grundläggande spårning har samma funktion och är mer informativ.

![Grafisk redigering jobbet strömmar vy](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Du kan visa från föregående skärmbild att mycket mer information finns tillgänglig i produktion jobbet dataströmmar vyn när du aktiverar utförlig loggning och spårning för grafiska runbook-flöden. Extra informationen kan vara nödvändigt för att felsöka problem med en runbook i produktionen och därför bör du bara aktivera det för detta ändamål och inte som en allmän regel. Spåra poster kan vara särskilt många. Du kan få två till fyra poster per aktivitet beroende på om du har konfigurerat grundläggande eller detaljerat spårning med grafisk runbook spårning. Om du behöver denna information för att följa förloppet för en runbook för felsökning kan kanske du vill behålla spårning avstängd.

**Utför följande steg om du vill aktivera aktivitetsnivå spårning:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Under **Processautomatisering**väljer **Runbooks** att öppna listan över runbooks.
3. Om du vill välja en grafisk runbook från listan med runbooks på sidan Runbooks.
4. Under **inställningar**, klickar du på **loggning och spårning**.
5. Klicka på loggning och spårning sidan under logga utförliga meddelanden **på** att aktivera utförlig loggning och ändra spårningsnivån till under aktivitetsnivå spårning **grundläggande** eller **detaljerad** baserat på nivån av spårning som du behöver.<br>
   
   ![Grafisk redigering loggning och spårning bladet](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure logganalys
Automatisering kan skicka runbook jobbet status och jobbstatus strömmar till logganalys-arbetsytan. Du kan göra följande med logganalys,

* Skaffa dig insikter om dina Automation-jobb 
* Utlösare som en e-post eller en avisering baserat på din runbook jobbets status (till exempel misslyckades eller pausas) 
* Skriva avancerade frågor över jobb-strömmar 
* Korrelera jobb över Automation-konton 
* Visualisera dina jobbhistorik över tid    

Mer information om hur du konfigurerar du integrering med logganalys samla in, korrelera och agera på jobbdata finns [vidarebefordra jobbstatus och jobbet strömmar från Automation till logganalys](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
* Information om hur du utformar och använda underordnade runbooks finns [underordnade runbooks i Azure Automation](automation-child-runbooks.md)

