---
title: Runbook-körning i Azure Automation
description: Beskriver information om hur en Runbook i Azure Automation bearbetas.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367150"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

Runbooks körs baserat på logiken som definierats i dem. Om en Runbook avbryts startar runbooken om i början. Med det här beteendet måste du skriva Runbooks som stöder omstart om det uppstår tillfälliga problem.

Om du startar en Runbook i Azure Automation skapas ett jobb. Ett jobb är en enskild körnings instans av runbooken. Varje jobb har åtkomst till Azure-resurser genom att ansluta till din Azure-prenumeration. Jobbet har bara åtkomst till resurser i ditt data Center om resurserna är tillgängliga från det offentliga molnet.

Azure Automation tilldelar en arbets rutin att köra varje jobb under Runbook-körningen. Medan arbets tagarna delas av många Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du har inte kontroll över vilka Worker Services ditt jobb begär.

När du visar listan över Runbooks i Azure Portal visas statusen för varje jobb som har startats för varje Runbook. Azure Automation lagrar jobb loggar i högst 30 dagar. 

Följande diagram visar livs cykeln för ett Runbook-jobb för [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks), [grafiska runbooks](automation-runbook-types.md#graphical-runbooks)och [PowerShell Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Jobb status – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Var du ska köra Runbooks

Runbooks i Azure Automation kan köras antingen i ett Azure-sandbox eller en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). De flesta Runbooks kan enkelt köras i en Azure-sandbox, en delad miljö som flera jobb kan använda. Jobb som använder samma Sandbox är begränsade till resurs begränsningarna i sand boxen.

Du kan använda en Hybrid Runbook Worker för att köra Runbooks direkt på den dator som är värd för rollen och mot lokala resurser i miljön. Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera tilldelade datorer.

I följande tabell visas några aktiviteter för Runbook-körningar med den rekommenderade körnings miljön som visas för var och en.

|Aktivitet|Bästa valet|Anteckningar|
|---|---|---|
|Integrera med Azure-resurser|Sand box för Azure|Azure är värd för Azure och det är enklare att autentisera. Om du använder en Hybrid Runbook Worker på en virtuell Azure-dator kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Få optimala prestanda för att hantera Azure-resurser|Sand box för Azure|Skriptet körs i samma miljö, vilket har mindre latens.|
|Minimera drifts kostnader|Sand box för Azure|Det finns ingen beräknings kapacitet och inget behov av en virtuell dator.|
|Kör tids krävande skript|Hybrid Runbook Worker|Azure-sand lådor har [begränsningar för resurser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagera med lokala tjänster|Hybrid Runbook Worker|Kan ha åtkomst direkt till värddatorn.|
|Kräv program vara från tredje part och körbara filer|Hybrid Runbook Worker|Du hanterar operativ systemet och kan installera program vara.|
|Övervaka en fil eller mapp med en Runbook|Hybrid Runbook Worker|Använd en [bevakare-aktivitet](automation-watchers-tutorial.md) på en hybrid Runbook Worker.|
|Köra ett resurs intensivt skript|Hybrid Runbook Worker| Azure-sand lådor har [begränsningar för resurser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Använda moduler med särskilda krav| Hybrid Runbook Worker|Några exempel är:</br> WinSCP – beroende av WinSCP. exe </br> Administrationsmodul – beroende av aktivering av IIS.|
|Installera en modul med ett installations program|Hybrid Runbook Worker|Moduler för sandbox måste ha stöd för kopiering.|
|Använd Runbooks eller moduler som kräver .NET Framework version som skiljer sig från 4.7.2|Hybrid Runbook Worker|Automation-sandboxs har .NET Framework 4.7.2 och det finns inget sätt att uppgradera den.|
|Kör skript som kräver höjning|Hybrid Runbook Worker|Sand boxen tillåter inte utökade privilegier. Med en Hybrid Runbook Worker kan du inaktivera UAC och använda **Invoke-Command** när du kör kommandot som kräver utökade privilegier.|
|Kör skript som kräver åtkomst till WMI|Hybrid Runbook Worker|Jobb som körs i begränsade lägen i molnet har inte åtkomst till WMI. |

## <a name="runbook-behavior"></a>Runbook-beteende

### <a name="creating-resources"></a>Skapa resurser

Om din Runbook skapar en resurs, bör skriptet kontrol lera om resursen redan finns innan du försöker skapa den. Här är ett grundläggande exempel.

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

### <a name="supporting-time-dependent-scripts"></a>Stöd för tids beroende skript

Dina Runbooks måste vara robusta och kunna hantera tillfälliga fel som kan orsaka att de startas om eller kraschar. Azure Automation försöker igen om en Runbook Miss lyckas.

Om din Runbook normalt körs inom en tids gräns, ska skriptet implementera logik för att kontrol lera körnings tiden. Den här kontrollen säkerställer att körning av åtgärder som t. ex. Start, avstängning eller skalning bara sker under vissa tidpunkter.

> [!NOTE]
> Den lokala tiden för Azures sand Box process är inställd på UTC. Beräkningar för datum och tid i dina Runbooks måste ta hänsyn till detta.

### <a name="tracking-progress"></a>Spårnings förlopp

Det är en bra idé att redigera dina runbooks för att vara modulära, strukturera Runbook-logik så att de kan återanvändas och startas om enkelt. Att spåra förloppet i en Runbook är ett bra sätt att se till att Runbook-logiken körs korrekt om det finns problem. Det är möjligt att spåra förloppet för en Runbook genom att använda en extern källa, till exempel ett lagrings konto, en databas eller delade filer. Du kan skapa logik i din Runbook för att först kontrol lera statusen för den senaste åtgärden som har vidtagits. Sedan, baserat på resultatet av kontrollen, kan logiken antingen hoppa över eller fortsätta med vissa uppgifter i runbooken.

### <a name="preventing-concurrent-jobs"></a>Förhindra samtidiga jobb

Vissa Runbooks fungerar konstigt om de körs över flera jobb samtidigt. I det här fallet är det viktigt att en Runbook implementerar logik för att avgöra om det redan finns ett jobb som körs. Här är ett grundläggande exempel.

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

För att hantera flera prenumerationer måste din Runbook använda cmdleten [disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) för att säkerställa att autentiserings kontexten inte hämtas från en annan Runbook som körs i samma sandbox. Runbooken använder också parametern`AzContext` i AZ-modulens cmdlets och skickar den till rätt kontext.

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

I det här avsnittet beskrivs några sätt att hantera undantag eller tillfälliga problem i dina runbooks.

#### <a name="erroractionpreference"></a>ErrorActionPreference

[Erroractionpreference satt](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) -variabeln avgör hur PowerShell svarar på ett icke-avslutande fel. Avslutande fel avslutas alltid och påverkas inte av *erroractionpreference satt*.

När en Runbook använder `ErrorActionPreference`kan ett vanligt icke-avslutande fel, till exempel **PathNotFound** från `Get-ChildItem` cmdlet, stoppa Runbook från att slutföras. I följande exempel visas användningen av `ErrorActionPreference`. Det slutgiltiga `Write-Output` kommandot körs aldrig, eftersom skriptet slutar.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Prova att fånga finally

[Prova Catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) används i PowerShell-skript för att hantera avslutande fel. Skriptet kan använda den här metoden för att fånga upp vissa undantag eller allmänna undantag. `catch`-instruktionen ska användas för att spåra eller försöka hantera fel. I följande exempel försöker hämta en fil som inte finns. Den fångar `System.Net.WebException` undantag och returnerar det sista värdet för alla andra undantag.

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan användas för att generera ett avslutande fel. Den här mekanismen kan vara användbar när du definierar din egen logik i en Runbook. Om skriptet uppfyller ett villkor som ska stoppas kan det använda `throw`-instruktionen för att stoppa. I följande exempel används den här instruktionen för att visa en obligatorisk funktions parameter.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Använda körbara filer eller anropande processer

Runbooks som körs i Azure-sandbox stöder inte anrops processer, till exempel körbara filer ( **. exe** -filer) eller under processer.  Orsaken till detta är att en Azure-Sandbox är en delad process som körs i en behållare som kanske inte har åtkomst till alla underliggande API: er. För scenarier som kräver tredjepartsprogram eller anrop till under processer rekommenderar vi att du kör en Runbook på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Åtkomst till enhets-och program egenskaper

Runbook-jobb som körs i Azure-sandbox har inte åtkomst till några enhets-eller program egenskaper. Det vanligaste API: et som används för att fråga prestanda värden i Windows är WMI, med några vanliga mått som minnes-och processor användning. Men det spelar ingen roll vilket API som används, eftersom jobb som körs i molnet inte har åtkomst till Microsofts implementering av webbaserad företags hantering (WBEM). Den här plattformen bygger på Common Information Model (CIM) och tillhandahåller bransch standarder för att definiera egenskaper för enheter och program.

## <a name="handling-errors"></a>Hantera fel

Dina Runbooks måste kunna hantera fel. PowerShell har två typer av fel, avslutande och icke-avslutande. Om du avslutar fel stoppas Runbook-körningen när de inträffar. Runbooken slutar med jobb statusen misslyckades.

Icke-avslutande fel tillåter att ett skript fortsätter även efter att det har inträffat. Ett exempel på ett icke-avslutande fel är en som inträffar när en Runbook använder `Get-ChildItem`-cmdlet med en sökväg som inte finns. PowerShell ser att sökvägen inte finns, genererar ett fel och fortsätter till nästa mapp. Felet i det här fallet anger inte status för Runbook-jobb till misslyckad och jobbet kan till och med slutföras. Om du vill tvinga en Runbook att stoppa vid ett icke-avslutande fel, kan du använda `-ErrorAction Stop` på cmdleten.

## <a name="handling-jobs"></a>Hanterings jobb

Du kan återanvända körnings miljön för jobb från samma Automation-konto. En enda Runbook kan ha många jobb på samma gång. Fler jobb som du kör samtidigt, desto oftare kan de skickas till samma sandbox.

Jobb som körs i samma sand Box process kan påverka varandra. Ett exempel är att köra cmdleten `Disconnect-AzAccount`. Körning av denna cmdlet kopplar från varje Runbook-jobb i den delade sand Box processen.

PowerShell-jobb som startas från en Runbook som körs i en Azure-sandbox kanske inte körs i det fullständiga språk läget. Mer information om PowerShell-språklägen finns i [PowerShell-språklägen](/powershell/module/microsoft.powershell.core/about/about_language_modes). Mer information om hur du interagerar med jobb i Azure Automation finns i [Hämta jobb status med PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Jobb status

I följande tabell beskrivs de status värden som är möjliga för ett jobb.

| Status | Beskrivning |
|:--- |:--- |
| Slutfört |Jobbet har slutförts. |
| Misslyckades |Det gick inte att kompilera en grafisk eller PowerShell-arbetsflöde Runbook. Det gick inte att starta PowerShell-skriptet eller så innehöll jobbet ett undantag. Se [Azure Automation Runbook-typer](automation-runbook-types.md).|
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom det nådde den [verkliga delnings](#fair-share) gränsen tre gånger och startade från samma kontroll punkt eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på att resurser i en automatiserings arbets uppgift ska bli tillgängliga så att de kan startas. |
| Startar |Jobbet har tilldelats en anställd och systemet startas. |
| Återupptar |Systemet återupptar jobbet när det har pausats. |
| Körs |Jobbet körs |
| Körs, väntar på resurser |Jobbet har inaktiverats på grund av att gränsen nåddes för den verkliga delningen. Den kommer snart att återupptas från den senaste kontroll punkten. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppas |Jobbet stoppas av systemet. |
| Uppehåll |Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en Runbook inte har en kontroll punkt börjar den från början. Om den har en kontroll punkt kan den startas igen och återupptas från den senaste kontroll punkten. Systemet pausar bara runbooken när ett undantag inträffar. Som standard är variabeln `ErrorActionPreference` inställd på Fortsätt, vilket indikerar att jobbet fortsätter att köras vid ett fel. Om variabeln ställs in på Avbryt pausas jobbet vid ett fel.  |
| Pausar |Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . Systemet försöker pausa jobbet på användarens begäran. Runbooken måste komma fram till nästa kontroll punkt innan den kan pausas. Om den redan har passerat den senaste kontroll punkten slutförs den innan den kan pausas. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Visa jobb status från Azure Portal

Du kan visa en sammanfattnings status för alla Runbook-jobb eller öka detalj nivån för ett särskilt Runbook-jobb i Azure Portal. Du kan också konfigurera integrering med din Log Analytics arbets yta för att vidarebefordra Runbook-jobbets status och jobb strömmar. Mer information om hur du integrerar med Azure Monitor loggar finns i [vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

Till höger om det valda Automation-kontot kan du se en översikt över alla Runbook-jobb under panelen **jobb statistik** .

![Panel för jobb statistik](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Den här panelen visar ett antal och en grafisk representation av jobb statusen för varje jobb som körs.

Om du klickar på panelen visas jobb sidan som innehåller en sammanfattande lista över alla jobb som körs. Den här sidan visar status, Runbook-namn, start tid och slut för ande tid för varje jobb.

![Sidan jobb för Automation-konto](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Du kan filtrera listan över jobb genom att välja **filter jobb**. Filtrera på en angiven Runbook, jobb status eller ett alternativ i list rutan och ange tidsintervallet för sökningen.

![Filtrera jobb status](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Du kan också Visa jobb sammanfattnings information för en angiven Runbook genom att välja denna Runbook från sidan Runbooks i ditt Automation-konto och sedan välja **jobb** panelen. Den här åtgärden visar sidan jobb. Härifrån kan du klicka på jobb posten för att visa information och utdata.

![Sidan jobb för Automation-konto](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Visa jobb sammanfattningen

I jobb sammanfattningen som beskrivs ovan kan du titta på en lista över alla jobb som har skapats för en viss Runbook och deras senaste status. Om du vill se detaljerad information och utdata för ett jobb klickar du på namnet i listan. I den detaljerade vyn av jobbet ingår värdena för de Runbook-parametrar som har angetts för jobbet.

Du kan använda följande steg för att se jobb för en runbook.

1. I Azure Portal väljer du **Automation** och väljer sedan namnet på ett Automation-konto.
2. Från hubben väljer du **Runbooks** under **process automatisering**.
3. På sidan Runbooks väljer du en Runbook i listan.
3. Klicka på panelen **jobb** på sidan för den valda Runbook-flödet.
4. Klicka på ett av jobben i listan och Visa information och utdata på sidan information om Runbook-jobb.

### <a name="retrieving-job-status-using-powershell"></a>Jobb status hämtas med PowerShell

Använd `Get-AzAutomationJob`-cmdlet för att hämta jobb som skapats för en Runbook och information om ett visst jobb. Om du startar en Runbook med PowerShell med `Start-AzAutomationRunbook`returneras det resulterande jobbet. Använd [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) för att hämta jobbets utdata.

Följande exempel hämtar det senaste jobbet för en exempel-Runbook och visar dess status, de värden som har angetts för Runbook-parametrarna och jobbets utdata.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

I följande exempel hämtas utdata för ett särskilt jobb och returnerar varje post. Om det finns ett undantag för en av posterna skriver skriptet detta undantag i stället för värdet. Det här beteendet är användbart, som undantag kan ge ytterligare information som kanske inte loggas normalt under utdata.

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

## <a name="getting-details-from-the-activity-log"></a>Hämta information från aktivitets loggen

Du kan hämta information om Runbook, till exempel den person eller det konto som startade runbooken, från aktivitets loggen för Automation-kontot. Följande PowerShell-exempel ger den senaste användaren att köra den angivna runbooken.

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

## <a name="fair-share"></a>Dela resurser mellan Runbooks

Om du vill dela resurser mellan alla Runbooks i molnet Azure Automation du tillfälligt tar bort eller stoppar jobb som har körts i mer än tre timmar. Jobb för [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) och [python-Runbooks](automation-runbook-types.md#python-runbooks) stoppas och startas inte om, och jobb statusen stoppas.

För tids krävande uppgifter rekommenderar vi att du använder en Hybrid Runbook Worker. Hybrid Runbook Worker begränsas inte av en rättvis resurs och har ingen begränsning för hur länge en Runbook kan köras. De andra jobb [gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) gäller för både Azure-Sandbox och hybrid Runbook Worker. Även om hybrid Runbook Worker inte begränsas av gränsen för gränsen på 3 timmar, bör du utveckla Runbooks som ska köras på de arbetare som har stöd för omstarter från oväntade problem med lokal infrastruktur.

Ett annat alternativ är att optimera en Runbook genom att använda underordnade Runbooks. Din Runbook kan till exempel loopa genom samma funktion på flera resurser, till exempel en databas åtgärd på flera databaser. Du kan flytta den här funktionen till en [underordnad Runbook](automation-child-runbooks.md) och låta din Runbook anropa den med hjälp av `Start-AzAutomationRunbook`. Underordnade Runbooks körs parallellt i separata processer.

Om underordnade Runbooks används minskar den totala tiden som den överordnade runbooken slutförs. Din Runbook kan använda `Get-AzAutomationJob` cmdlet för att kontrol lera jobb status för en underordnad Runbook om den fortfarande har åtgärder att utföra efter att den underordnade har slutförts.

## <a name="next-steps"></a>Nästa steg

* Mer information om de metoder som kan användas för att starta en Runbook i Azure Automation finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
