---
title: Körning av Runbook i Azure Automation
description: Beskriver information om hur en runbook i Azure Automation bearbetas.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 1907eb7cde482927ee8e6b0a2522158f05c1808f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010944"
---
# <a name="runbook-execution-in-azure-automation"></a>Körning av Runbook i Azure Automation

Runbooks körs baserat på logiken som definierats inuti dem. Om en runbook avbryts startas runbooken om i början. Detta kräver att du skriver runbooks som stöder startas om om tillfälliga problem uppstår.

Starta en runbook i Azure Automation skapar ett jobb, vilket är en enda körningsinstans av runbook. Varje jobb har åtkomst till Azure-resurser genom att göra en anslutning till din Azure-prenumeration. Jobbet har bara åtkomst till resurser i ditt datacenter om dessa resurser är tillgängliga från det offentliga molnet.

Azure Automation tilldelar en arbetare att köra varje jobb under körning av runbook. Medan arbetare delas av många Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du har inte kontroll över vilka arbetartjänster din jobbbegäran.

När du visar listan över runbooks i Azure-portalen visas status för varje jobb som har startats för varje runbook. Azure Automation lagrar jobbloggar i högst 30 dagar. 

I följande diagram visas livscykeln för ett runbook-jobb för [PowerShell-runbooks,](automation-runbook-types.md#powershell-runbooks) [grafiska runbooks](automation-runbook-types.md#graphical-runbooks)och [PowerShell Workflow runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Jobbstatus - PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Var du kan köra dina runbooks

Runbooks i Azure Automation kan köras på antingen en Azure-sandlåda eller en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). De flesta runbooks kan enkelt köras i en Azure sandlåda, en delad miljö som flera jobb kan använda. Jobb som använder samma sandlåda är bundna av sandlådans resursbegränsningar.

Du kan använda en Hybrid Runbook Worker för att köra runbooks direkt på den dator som är värd för rollen och mot lokala resurser i miljön. Azure Automation lagrar och hanterar runbooks och levererar dem sedan till en eller flera tilldelade datorer.

I följande tabell visas några körningsuppgifter för runbook med den rekommenderade körningsmiljön som anges för varje.

|Aktivitet|Bästa val|Anteckningar|
|---|---|---|
|Integrera med Azure-resurser|Begränsat till Azure|Autentiseringen är enklare i Azure. Om du använder en Hybrid Runbook Worker på en Virtuell Azure-dator kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Få optimal prestanda för att hantera Azure-resurser|Begränsat till Azure|Skript körs i samma miljö, som har mindre svarstid.|
|Minimera driftskostnaderna|Begränsat till Azure|Det finns ingen beräkningsomkostnader och inget behov av en virtuell dator.|
|Köra tidskrävande skript|Hybrid Runbook Worker|Azure sandlådor har [begränsningar för resurser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagera med lokala tjänster|Hybrid Runbook Worker|Kan få åtkomst direkt till värddatorn.|
|Kräv programvara från tredje part och körbara filer|Hybrid Runbook Worker|Du hanterar operativsystemet och kan installera programvara.|
|Övervaka en fil eller mapp med en runbook|Hybrid Runbook Worker|Använd en [Watcher-uppgift](automation-watchers-tutorial.md) på en Hybrid Runbook Worker.|
|Köra ett resursintensivt skript|Hybrid Runbook Worker| Azure sandlådor har [begränsningar för resurser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Använda moduler med specifika krav| Hybrid Runbook Worker|Några exempel är:</br> WinSCP - beroende av winscp.exe </br> IISAdministration - beroende av aktivering av IIS.|
|Installera en modul med ett installationsprogram|Hybrid Runbook Worker|Moduler för sandlåda måste stödja kopiering.|
|Använd runbooks eller moduler som kräver .NET Framework-version som skiljer sig från 4.7.2|Hybrid Runbook Worker|Automation sandlådor har .NET Framework 4.7.2, och det finns inget sätt att uppgradera den.|
|Kör skript som kräver höjd|Hybrid Runbook Worker|Sandlådor tillåter inte höjd. Med en Hybrid Runbook Worker kan du stänga av UAC och använda **Anropa kommando** när du kör kommandot som kräver höjd.|
|Kör skript som kräver åtkomst till WMI|Hybrid Runbook Worker|Jobb som körs i sandlådor i molnet har inte åtkomst till WMI. |

## <a name="runbook-behavior"></a>Runbook-beteende

### <a name="creating-resources"></a>Skapa resurser

Om runbooken skapar en resurs bör skriptet kontrollera om resursen redan finns innan den försöker skapas. Här är ett grundläggande exempel.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Stöd för tidsberoende skript

Runbooks måste vara robusta och kunna hantera tillfälliga fel som kan orsaka att de startas om eller misslyckas. Om en runbook misslyckas försöker Azure Automation den.

Om runbook normalt körs inom ett tidsvillkor, har skriptet implementera logik för att kontrollera körningstiden. Den här kontrollen säkerställer att åtgärder som start, avstängning eller utskalning endast körs under specifika tider.

> [!NOTE]
> Den lokala tiden på Azure-sandlådeprocessen är inställd på UTC. Beräkningar för datum och tid i dina runbooks måste ta hänsyn till detta.

### <a name="tracking-progress"></a>Spåra förlopp

Det är en god praxis att skriva dina runbooks att vara modulära i naturen, strukturera runbook logik så att den kan återanvändas och startas om lätt. Att spåra förloppet i en runbook är ett bra sätt att se till att runbook-logiken körs korrekt om det finns problem. Det är möjligt att spåra förloppet för en runbook med hjälp av en extern källa, till exempel ett lagringskonto, en databas eller delade filer. Du kan skapa logik i runbooken för att först kontrollera tillståndet för den senaste åtgärden som vidtogs. Baserat på resultatet av kontrollen kan logiken antingen hoppa över eller fortsätta specifika uppgifter i runbooken.

### <a name="preventing-concurrent-jobs"></a>Förhindra samtidiga jobb

Vissa runbooks beter sig konstigt om de körs över flera jobb samtidigt. I det här fallet är det viktigt för en runbook att implementera logik för att avgöra om det redan finns ett jobb som körs. Här är ett grundläggande exempel.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Arbeta med flera prenumerationer

För att hantera flera prenumerationer måste runbooken använda cmdleten [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) för att säkerställa att autentiseringskontexten inte hämtas från en annan runbook som körs i samma sandlåda. Runbook använder också`AzContext` parametern på Az-modulen cmdlets och skickar den rätt sammanhang.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Hantering av undantag

I det här avsnittet beskrivs några sätt att hantera undantag eller återkommande problem i runbooks.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Variabeln [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) avgör hur PowerShell svarar på ett fel som inte avslutas. Avsluta fel avslutas alltid och påverkas inte av *ErrorActionPreference*.

När runbooken `ErrorActionPreference`använder stoppar ett normalt icke-avslutande fel, `Get-ChildItem` till exempel **PathNotFound** från cmdleten, runbooken från att slutföras. Följande exempel visar användningen `ErrorActionPreference`av . Det `Write-Output` slutliga kommandot körs aldrig, eftersom skriptet stoppas.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Prova Catch Slutligen

[Prova Catch Slutligen](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) används i PowerShell-skript för att hantera avslutande fel. Skriptet kan använda den här mekanismen för att fånga specifika undantag eller allmänna undantag. Satsen `catch` ska användas för att spåra eller försöka hantera fel. I följande exempel försöker du hämta en fil som inte finns. Undantaget fångars `System.Net.WebException` och returnerar det sista värdet för alla andra undantag.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Kasta

[Kasta](/powershell/module/microsoft.powershell.core/about/about_throw) kan användas för att generera ett avslutande fel. Den här mekanismen kan vara användbar när du definierar din egen logik i en runbook. Om skriptet uppfyller ett kriterium som `throw` ska stoppa det, kan det använda satsen för att stoppa. I följande exempel används den här satsen för att visa en obligatorisk funktionsparameter.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Använda körbara filer eller anropa processer

Runbooks som körs i Azure-sandlådor stöder inte anropande processer, till exempel körbara filer (**.exe-filer)** eller underprocesser.  Anledningen till detta är att en Azure-sandlåda är en delad process som körs i en behållare som kanske inte har åtkomst till alla underliggande API:er. För scenarier som kräver programvara från tredje part eller anrop till underprocesser rekommenderar vi att du kör en runbook på en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Komma åt enhets- och applikationsegenskaper

Runbook-jobb som körs i Azure-sandlådor har inte åtkomst till någon enhet eller programegenskaper. Det vanligaste API:et som används för att fråga efter prestandamått i Windows är WMI, där några av de vanligaste måtten är minnes- och CPU-användning. Det spelar dock ingen roll vilket API som används, eftersom jobb som körs i molnet inte har åtkomst till Microsoft-implementeringen av Web-Based Enterprise Management (WBEM). Den här plattformen bygger på common information model (CIM), som tillhandahåller branschstandarder för att definiera enhets- och applikationsegenskaper.

## <a name="handling-errors"></a>Hantera fel

Dina runbooks måste kunna hantera fel. PowerShell har två typer av fel, avslutande och icke-avslutande. Om du avslutar fel stoppa körningen av runbook när de inträffar. Runbooken stoppas med en jobbstatus misslyckades.

Om du inte avslutar fel kan ett skript fortsätta även efter att de inträffat. Ett exempel på ett icke-avslutande fel är ett som `Get-ChildItem` uppstår när en runbook använder cmdleten med en sökväg som inte finns. PowerShell ser att sökvägen inte finns, genererar ett fel och fortsätter till nästa mapp. Felet i det här fallet anger inte status för körningsjobbstatus till Misslyckades och jobbet kan till och med slutföras. Om du vill tvinga en runbook att stoppas `-ErrorAction Stop` på ett fel som inte är avslutande kan du använda på cmdleten.

## <a name="handling-jobs"></a>Hantera jobb

Du kan återanvända körningsmiljön för jobb från samma Automation-konto. En enda runbook kan ha många jobb som körs samtidigt. Ju fler jobb du kör samtidigt, desto oftare kan de skickas till samma sandlåda.

Jobb som körs i samma sandlådeprocess kan påverka varandra. Ett exempel är `Disconnect-AzAccount` att köra cmdlet. Körning av den här cmdleten kopplar från varje runbook-jobb i den delade sandlådeprocessen.

PowerShell-jobb startade från en runbook som körs i en Azure-sandlåda kanske inte körs i fullständigt språkläge. Mer information om Språklägen i PowerShell finns i [Språklägen för PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Mer information om hur du interagerar med jobb i Azure Automation finns i [Hämta jobbstatus med PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Jobbstatus

I följande tabell beskrivs de statusar som är möjliga för ett jobb.

| Status | Beskrivning |
|:--- |:--- |
| Slutfört |Jobbet har slutförts. |
| Misslyckades |Det gick inte att kompilera en grafisk eller PowerShell-arbetsflödeskörning. Det gick inte att starta en PowerShell-skriptkörning eller så fick jobbet ett undantag. Se [Azure Automation runbook typer](automation-runbook-types.md).|
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom det nådde gränsen [för skälig resurs](#fair-share) tre gånger och startade från samma kontrollpunkt eller från början av körningsboken varje gång. |
| I kö |Jobbet väntar på att resurser på en Automation-arbetare ska bli tillgängliga så att det kan startas. |
| Startar |Jobbet har tilldelats en anställd och systemet startar det. |
| Återupptar |Systemet återupptar jobbet efter att det har avbrutits. |
| Körs |Jobbet körs |
| Köra, vänta på resurser |Jobbet har lossats eftersom det nått gränsen för skälig andel. Det kommer att återupptas inom kort från sin sista kontrollpunkt. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppas |Systemet stoppar jobbet. |
| Inaktiverad |Gäller endast [för grafiska och PowerShell-arbetsflödeskörningar.](automation-runbook-types.md) Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en runbook inte har en kontrollpunkt börjar den från början. Om den har en kontrollpunkt kan den starta igen och återupptas från den senaste kontrollpunkten. Programmet pausar bara körningsboken när ett undantag inträffar. Som standard `ErrorActionPreference` är variabeln inställd på Fortsätt, vilket anger att jobbet fortsätter att köras på ett fel. Om inställningsvariabeln är inställd på Stopp avbryts jobbet vid ett fel.  |
| Pausar |Gäller endast [för grafiska och PowerShell-arbetsflödeskörningar.](automation-runbook-types.md) Systemet försöker avbryta jobbet på begäran av användaren. Runbooken måste nå nästa kontrollpunkt innan den kan pausas. Om den redan har passerat sin sista kontrollpunkt slutförs den innan den kan avbrytas. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Visa jobbstatus från Azure-portalen

Du kan visa en sammanfattad status för alla runbook-jobb eller detaljgranska information om ett visst runbook-jobb i Azure-portalen. Du kan också konfigurera integrering med logganalysarbetsytan för att vidarebefordra körningsjobbstatus och jobbströmmar. Mer information om hur du integrerar med Azure Monitor-loggar finns i [Vidarebefordra jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).

Till höger om ditt valda Automation-konto kan du se en sammanfattning av alla runbook-jobb under panelen **Jobbstatistik.**

![Panelen Jobbstatistik](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Den här panelen visar en antal och grafisk representation av jobbstatusen för varje utförd jobb.

Om du klickar på panelen visas sidan Jobb, som innehåller en sammanfattad lista över alla jobb som körs. På den här sidan visas status, runbooknamn, starttid och slutförandetid för varje jobb.

![Sidan Jobb för automatiseringskonto](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Du kan filtrera listan över jobb genom att välja **Filterjobb**. Filtrera på en viss runbook, jobbstatus eller ett val från listrutan och ange tidsintervallet för sökningen.

![Filtrera jobbstatus](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Du kan också visa jobbsammanfattningsinformation för en viss runbook genom att välja runbooken på sidan Runbooks i ditt Automation-konto och sedan välja panelen **Jobb.** Den här åtgärden visar sidan Jobb. Härifrån kan du klicka på jobbposten för att visa dess detaljer och utdata.

![Sidan Jobb för automatiseringskonto](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Visa jobbsammanfattningen

Med jobbsammanfattningen som beskrivs ovan kan du titta på en lista över alla jobb som har skapats för en viss runbook och deras senaste status. Klicka på namnet i listan om du vill se detaljerad information och utdata för ett jobb. Den detaljerade vyn för jobbet innehåller värdena för runbook-parametrarna som har angetts för det jobbet.

Du kan använda följande steg för att se jobb för en runbook.

1. I Azure-portalen väljer du **Automatisering** och väljer sedan namnet på ett Automation-konto.
2. Välj **Runbooks** under **Process Automation**i navet .
3. Välj en runbook i listan på sidan Runbooks.
3. Klicka på panelen **Jobb** på sidan för den valda runbooken.
4. Klicka på ett av jobben i listan och visa dess information och utdata på sidan för runbook-jobbinformation.

### <a name="retrieving-job-status-using-powershell"></a>Hämta jobbstatus med PowerShell

Använd `Get-AzAutomationJob` cmdlet för att hämta de jobb som skapats för en runbook och information om ett visst jobb. Om du startar en runbook `Start-AzAutomationRunbook`med PowerShell med, returnerar den det resulterande jobbet. Använd [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) för att hämta jobbutdata.

I följande exempel får det sista jobbet för en exempelkörningsbok och dess status, de värden som anges för runbook-parametrarna och projektutdata.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

I följande exempel hämtas utdata för ett visst jobb och varje post returneras. Om det finns ett undantag för en av posterna skriver skriptet undantaget i stället för värdet. Det här problemet är användbart eftersom undantag kan ge ytterligare information som kanske inte loggas normalt under utdata.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="getting-details-from-the-activity-log"></a>Hämta information från aktivitetsloggen

Du kan hämta runbook-information, till exempel personen eller kontot som startade runbooken, från aktivitetsloggen för Automation-kontot. I följande PowerShell-exempel får den sista användaren att köra den angivna runbooken.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Dela resurser mellan runbooks

Om du vill dela resurser mellan alla runbooks i molnet tas Azure Automation tillfälligt bort eller stoppar alla jobb som har körts i mer än tre timmar. Jobb för [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) och [Python-runbooks stoppas](automation-runbook-types.md#python-runbooks) och startas inte om och jobbstatusen stoppas.

För tidskrävande uppgifter rekommenderas att använda en Hybrid Runbook Worker. Hybrid Runbook Workers begränsas inte av skälig andel och har ingen begränsning av hur länge en runbook kan köras. De andra [jobbgränserna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) gäller både Azure-sandlådor och Hybrid Runbook Workers. Hybrid Runbook Workers begränsas inte av gränsen för rättvis delning på tre timmar, men du bör utveckla runbooks för att köras på de arbetare som stöder omstarter från oväntade lokala infrastrukturproblem.

Ett annat alternativ är att optimera en runbook med hjälp av underordnade runbooks. Runbook kan till exempel gå igenom samma funktion på flera resurser, till exempel en databasåtgärd i flera databaser. Du kan flytta den här funktionen till en underordnad `Start-AzAutomationRunbook` [runbook](automation-child-runbooks.md) och låta runbooken anropa den med . Underordnade runbooks körs parallellt i separata processer.

Om du använder underordnade runbooks minskar den totala tiden för den överordnade runbooken att slutföras. Runbook kan `Get-AzAutomationJob` använda cmdlet för att kontrollera jobbstatus för en underordnad runbook om den fortfarande har åtgärder att utföra när barnet är klar.

## <a name="next-steps"></a>Nästa steg

* Mer information om de metoder som kan användas för att starta en runbook i Azure Automation finns [i Starta en runbook i Azure Automation](automation-starting-a-runbook.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
