---
title: Runbook-körning i Azure Automation
description: Beskriver information om hur en Runbook i Azure Automation bearbetas.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01a321503a2c55bfc28720675932e6813cdab320
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850593"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

När du startar en Runbook i Azure Automation skapas ett jobb. Ett jobb innebär ett utförande av en runbookinstans. En Azure Automation arbetare tilldelas för att köra varje jobb. Medan arbets tagarna delas av många Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du har inte kontroll över vilka Worker-tjänster som begär ditt jobb. En enda Runbook kan ha många jobb på samma gång. Körnings miljön för jobb från samma Automation-konto kan återanvändas. Fler jobb som du kör samtidigt, desto oftare kan de skickas till samma sandbox. Jobb som körs i samma sand Box process kan påverka varandra, ett exempel är att `Disconnect-AzureRMAccount` köra cmdleten. Om du kör denna cmdlet kopplas varje Runbook-jobb bort i den delade sand Box processen. När du visar listan över Runbooks i Azure Portal visas status för alla jobb som har startats för varje Runbook. Du kan visa listan över jobb för varje Runbook för att spåra status för varje. Jobb loggar lagras i högst 30 dagar. En beskrivning av [jobb](#job-statuses)status för olika jobb.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Följande diagram visar livs cykeln för ett Runbook-jobb för [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks), [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) och [PowerShell Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Jobb status – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

Dina jobb har åtkomst till dina Azure-resurser genom att ansluta till din Azure-prenumeration. De har bara åtkomst till resurser i ditt data Center om resurserna är tillgängliga från det offentliga molnet.

## <a name="where-to-run-your-runbooks"></a>Var du ska köra Runbooks

Runbooks i Azure Automation kan köras antingen i ett begränsat läge i Azure eller ett [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). En sandbox är en delad miljö i Azure som kan användas av flera jobb. Jobb som använder samma Sandbox är begränsade till resurs begränsningarna i sand boxen. Hybrid Runbook Worker kan köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer. De flesta Runbooks kan enkelt köras i sand boxen Azure. Det finns vissa scenarier där du kan välja en hybrid Runbook i ett Azure-sandbox för att köra din Runbook. I följande tabell finns en lista över några exempel scenarier:

|Uppgift|Bästa valet|Anteckningar|
|---|---|---|
|Integrera med Azure-resurser|Sand box för Azure|Azure är värd för Azure och det är enklare att autentisera. Om du använder en Hybrid Runbook Worker på en virtuell Azure-dator kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimala prestanda för att hantera Azure-resurser|Sand box för Azure|Skriptet körs i samma miljö, vilket i sin tur har mindre latens|
|Minimera drifts kostnader|Sand box för Azure|Det finns ingen beräknings omkostnader, inget behov av en virtuell dator|
|Tids krävande skript|Hybrid Runbook Worker|Azure-sand lådor har [begränsade resurser](../azure-subscription-service-limits.md#automation-limits)|
|Interagera med lokala tjänster|Hybrid Runbook Worker|Kan ha åtkomst direkt till värd datorn|
|Kräv program vara från tredje part och körbara filer|Hybrid Runbook Worker|Du hanterar operativ systemet och kan installera program vara|
|Övervaka en fil eller mapp med en Runbook|Hybrid Runbook Worker|Använda en [bevakare-uppgift](automation-watchers-tutorial.md) i en hybrid Runbook Worker|
|Resurs intensivt skript|Hybrid Runbook Worker| Azure-sand lådor har [begränsade resurser](../azure-subscription-service-limits.md#automation-limits)|
|Använda moduler med särskilda krav| Hybrid Runbook Worker|Några exempel är:</br> **WinSCP** – beroende av WinSCP. exe </br> **Administrationsmodul** – kräver att IIS aktive ras|
|Installations modul som kräver installations program|Hybrid Runbook Worker|Moduler för sandbox måste vara copiable|
|Använda Runbooks eller moduler som kräver .NET Framework skiljer sig från 4.7.2|Hybrid Runbook Worker|Automation-sandboxs har .NET Framework 4.7.2 och det finns inget sätt att uppgradera det|
|Skript som kräver höjning|Hybrid Runbook Worker|Sand boxen tillåter inte utökade privilegier. Lös detta genom att använda en hybrid Runbook Worker och du kan stänga av UAC och använda `Invoke-Command` när du kör kommandot som kräver utökade privilegier|
|Skript som kräver åtkomst till WMI|Hybrid Runbook Worker|Jobb som körs i begränsade lägen i molnet [har inte åtkomst till WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook-beteende

Runbooks körs baserat på den logik som definieras i dem. Om en Runbook avbryts startar runbooken om i början. Det här beteendet kräver att Runbooks skrivs på ett sätt där de har stöd för omstart om det finns tillfälliga problem.

PowerShell-jobb som startats från en Runbook kördes i en Azure-sandbox kanske inte körs i det fullständiga språk läget. Mer information om PowerShell-språklägen finns i [PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes)-språklägen. Mer information om hur du interagerar med jobb i Azure Automation finns i [Hämta jobb status med PowerShell](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Skapa resurser

Om du skapar resurser i skriptet bör du kontrol lera om resursen redan finns innan du försöker skapa den igen. Ett grundläggande exempel visas i följande exempel:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Tids beroende skript

Noga överväganden bör göras vid redigering av Runbooks. Som tidigare nämnts måste Runbooks skapas på ett sätt som är robust och kan hantera tillfälliga fel som kan orsaka att runbooken startas om eller Miss lyckas. Om en Runbook Miss lyckas görs ett nytt försök. Om en Runbook normalt körs inom en tids gräns, ska logiken för att kontrol lera körnings tiden implementeras i runbooken för att säkerställa att åtgärder som starta, Stäng ned eller skala ut körs bara under vissa tider.

> [!NOTE]
> Den lokala tiden för Azures sand Box process är inställd på UTC-tid. Beräkningar för datum och tid i dina Runbooks måste ta hänsyn till detta.

### <a name="tracking-progress"></a>Spårnings förlopp

Det är en bra idé att skapa Runbooks för att vara modulärt av natur. Det innebär att du strukturerar logiken i runbooken så att den kan återanvändas och startas om enkelt. Att spåra förloppet i en Runbook är ett bra sätt att se till att logiken i en runbook körs på rätt sätt om det uppstår problem. Några möjliga sätt att följa förloppet för runbooken är genom att använda en extern källa, till exempel lagrings konton, en databas eller delade filer. Genom att spåra statusen externt kan du skapa logik i din Runbook för att först kontrol lera status för den senaste åtgärden som Runbook vidtog. Sedan, baserat på resultaten, kan du antingen hoppa över eller fortsätta med vissa uppgifter i runbooken.

### <a name="prevent-concurrent-jobs"></a>Förhindra samtidiga jobb

Vissa Runbooks kan fungera konstigt om de körs över flera jobb samtidigt. I det här fallet är det viktigt att implementera logik för att kontrol lera om det redan finns ett jobb i Runbook som körs. Ett grundläggande exempel på hur du kan göra detta visas i följande exempel:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
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

När du redigerar Runbooks som hanterar flera prenumerationer måste din Runbook använda cmdleten [disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) för att säkerställa att din autentisering inte hämtas från en annan Runbook som kan köras i samma sandbox. Du måste sedan använda `-AzureRmContext` parametern på dina `AzureRM` cmdlets och skicka den till rätt kontext.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Hantering av undantag

När du redigerar skript är det viktigt att kunna hantera undantag och potentiella tillfälliga fel. Följande är några olika sätt att hantera undantag eller tillfälliga problem med dina runbooks:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Variabeln [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) inställning avgör hur PowerShell svarar på ett icke-avslutande fel. Avslutande fel påverkas inte av `$ErrorActionPreference`, de avslutas alltid. Genom att `$ErrorActionPreference`använda kan ett vanligt icke-avslutande fel som `PathNotFound` från `Get-ChildItem` cmdleten stoppa Runbook från att slutföras. Följande exempel visar hur du `$ErrorActionPreference`använder. Den sista `Write-Output` raden körs aldrig när skriptet stoppas.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Prova att fånga finally

[Försök att fånga](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) används i PowerShell-skript för att hjälpa dig att hantera fel. Genom att använda try-catch kan du fånga upp vissa undantag eller allmänna undantag. Catch-instruktionen ska användas för att spåra fel eller som används för att försöka hantera felet. I följande exempel försöker hämta en fil som inte finns. Det fångar `System.Net.WebException` undantaget, om det fanns ett annat undantag returneras det sista värdet.

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

#### <a name="throw"></a>Genereras

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan användas för att generera ett avslutande fel. Detta kan vara användbart när du definierar din egen logik i en Runbook. Om ett visst villkor uppfylls som stoppar skriptet kan du använda `throw` för att stoppa skriptet. I följande exempel visas en funktions parameter för Machine som krävs `throw`med hjälp av.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Använda körbara filer eller anropande processer

Runbooks som körs i Azure-sandbox stöder inte anrop av processer (till exempel en. exe eller under process. Call). Detta beror på att Azure-sand lådor är delade processer som körs i behållare, som kanske inte har åtkomst till alla underliggande API: er. För scenarier där du behöver program vara från tredje part eller uppringning av under processer, rekommenderar vi att du kör runbooken på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Egenskaper för enhet och program

Runbook-jobb som körs i Azure-sandbox har inte åtkomst till några enhets-eller program egenskaper. Det vanligaste API: et som används för att fråga prestanda värden i Windows är WMI. Några av dessa vanliga mått är minne och CPU-användning. Men det spelar ingen roll vilket API som används. Jobb som körs i molnet har inte åtkomst till Microsofts implementering av webbaserad företags hantering (WBEM), som bygger på Common Information Model (CIM), som är bransch standarder för att definiera egenskaper för enheter och program.

## <a name="job-statuses"></a>Jobb status

Följande tabell beskriver de olika statuslägen som är möjliga för ett jobb. PowerShell har två typer av fel, avslutande och icke-avslutande fel. Om du avbryter fel anges Runbook- statusen till misslyckad om de inträffar. Icke-avslutande fel tillåter att skriptet fortsätter även efter att det har inträffat. Ett exempel på ett icke-avslutande fel är att `Get-ChildItem` använda cmdleten med en sökväg som inte finns. PowerShell ser att sökvägen inte finns, genererar ett fel och fortsätter till nästa mapp. Det här felet skulle **inte** ange Runbook-statusen Misslyckad och kan markeras som **slutförd**. Om du vill tvinga en Runbook att stoppa vid ett icke-avslutande fel, kan du `-ErrorAction Stop` använda på-cmdleten.

| Status | Beskrivning |
|:--- |:--- |
| Slutfört |Jobbet har slutförts. |
| Misslyckad |Runbook kunde inte kompileras för [grafiska och PowerShell-arbetsflöden](automation-runbook-types.md). För [PowerShell-skript Runbooks](automation-runbook-types.md)kunde Runbook inte starta eller så innehöll jobbet ett undantag. |
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom det nådde den [verkliga delnings](#fair-share) gränsen tre gånger och startade från samma kontroll punkt eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på att resurser från en Automation arbetare blir tillgängliga så det kan starta. |
| Startar |Jobbet har tilldelats en anställd och systemet startas. |
| Återupptar |Systemet återupptar jobbet när det har pausats. |
| Körs |Jobbet körs |
| Körs, väntar på resurser |Jobbet har inaktiverats på grund av att gränsen nåddes för den [verkliga delningen](#fair-share) . Den återupptas strax från den senaste kontroll punkten. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppar |Jobbet stoppas av systemet. |
| Uppehåll |Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en Runbook inte har en kontroll punkt börjar den från början av runbooken. Om den har en kontroll punkt kan den startas igen och återupptas från den senaste kontroll punkten. Runbooken pausas bara av systemet när ett undantag inträffar. Som standard är Erroractionpreference satt inställd på att **fortsätta**, vilket innebär att jobbet fortsätter att köras vid ett fel. Om den här preferens variabeln är inställd på **Avbryt**pausas jobbet vid ett fel. Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . |
| Pausar |Systemet försöker pausa jobbet på användarens begäran. Runbooken måste komma fram till nästa kontroll punkt innan den kan pausas. Om den redan har passerat den senaste kontroll punkten slutförs den innan den kan pausas. Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visa jobb status från Azure Portal

Du kan visa en sammanfattnings status för alla Runbook-jobb eller öka detalj nivån för ett särskilt Runbook-jobb i Azure Portal. Du kan också konfigurera integrering med din Log Analytics arbets yta för att vidarebefordra Runbook-jobbets status och jobb strömmar. Mer information om hur du integrerar med Azure Monitor loggar finns i [vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Översikt över Automation Runbook-jobb

Till höger om det valda Automation-kontot kan du se en översikt över alla Runbook-jobb på panelen **jobb statistik** .

![Panel för jobb statistik](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Den här panelen visar ett antal och en grafisk representation av jobb status för alla jobb som körs.

Om du klickar på panelen visas **jobb** sidan som innehåller en sammanfattande lista över alla jobb som körs. Den här sidan visar status, start tider och slut för ande tider.

![Sidan jobb för Automation-konto](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Du kan filtrera listan över jobb genom att välja **filtrera jobb** och filtrera på en angiven Runbook, jobb status eller i list rutan och tidsintervallet att söka inom.

![Filtrera jobb status](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Du kan också Visa jobb sammanfattnings information för en angiven Runbook genom att välja denna Runbook från sidan **Runbooks** i ditt Automation-konto och sedan välja **jobb** panelen. Den här åtgärden visar **jobb** sidan och därifrån kan du klicka på jobb posten för att visa information och utdata.

![Sidan jobb för Automation-konto](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Jobbsammanfattning

Du kan visa en lista över alla jobb som har skapats för en viss Runbook och deras senaste status. Du kan filtrera listan efter jobb status och datum intervallet för den senaste ändringen i jobbet. Om du vill visa detaljerad information och utdata klickar du på namnet på ett jobb. Jobbets detaljvy inkluderar värden för runbookparametrar som gavs av det jobbet.

Du kan använda följande steg för att se jobb för en runbook.

1. I Azure Portal väljer du **Automation** och väljer sedan namnet på ett Automation-konto.
2. Från hubben väljer du **Runbooks** och på sidan **Runbooks** väljer du sedan en Runbook i listan.
3. Klicka på panelen **jobb** på sidan för den valda Runbook-flödet.
4. Klicka på ett av jobben i listan och på sidan information om Runbook-jobb kan du Visa information och utdata.

## <a name="retrieving-job-status-using-powershell"></a>Jobb status hämtas med PowerShell

Du kan använda [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) för att hämta jobb som skapats för en Runbook och information om ett visst jobb. Om du startar en Runbook med PowerShell med [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), returnerar den det resulterande jobbet. Använd [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) för att hämta jobbets utdata.

Följande exempel kommandon hämtar det senaste jobbet för en exempel-Runbook och visar dess status, de värden som har angetts för Runbook-parametrarna och utdata från jobbet.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

I följande exempel hämtas utdata för ett särskilt jobb och returnerar varje post. Om det uppstod ett undantag för en av posterna skrivs undantaget ut i stället för värdet. Det här beteendet är användbart eftersom undantag kan ge ytterligare information, som kanske inte loggas normalt under utdata.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>Hämta information från aktivitets loggen

Andra uppgifter, till exempel den person eller det konto som startade runbooken, kan hämtas från aktivitets loggen för Automation-kontot. Följande PowerShell-exempel ger den senaste användaren att köra runbooken i fråga:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Skälig delning

Om du vill dela resurser mellan alla Runbooks i molnet Azure Automation du tillfälligt tar bort eller stoppar jobb som har körts i mer än tre timmar. Jobb för [PowerShell-baserade Runbooks](automation-runbook-types.md#powershell-runbooks) och [python-Runbooks](automation-runbook-types.md#python-runbooks) stoppas och startas inte om, och jobb status visas som stoppad.

För tids krävande uppgifter rekommenderar vi att du använder en [hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Hybrid Runbook Worker begränsas inte av en rättvis resurs och har ingen begränsning för hur länge en Runbook kan köras. De andra jobb [gränserna](../azure-subscription-service-limits.md#automation-limits) gäller för både Azure-Sandbox och hybrid Runbook Worker. Även om hybrid Runbook Worker inte begränsas av den högsta gränsen på 3 timmar, bör Runbooks som körs på dem utvecklas för att stödja omstarts beteenden från oväntade problem med lokal infrastruktur.

Ett annat alternativ är att optimera runbooken genom att använda underordnade Runbooks. Om din Runbook upprepas genom samma funktion på flera resurser, till exempel en databas åtgärd på flera databaser, kan du flytta den funktionen till en underordnad [Runbook](automation-child-runbooks.md) och anropa den med cmdleten [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Var och en av dessa underordnade Runbooks körs parallellt i separata processer. Det här beteendet minskar den totala tiden som den överordnade runbooken slutförs. Du kan använda cmdleten [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) i din Runbook för att kontrol lera jobb status för varje underordnat objekt om det finns åtgärder som utförs när den underordnade Runbook-flödet har slutförts.

## <a name="next-steps"></a>Nästa steg

* Mer information om de olika metoder som kan användas för att starta en Runbook i Azure Automation finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md)
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview)-dokumenten.
