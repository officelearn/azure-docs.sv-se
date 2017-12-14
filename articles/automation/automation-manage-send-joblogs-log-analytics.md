---
title: Vidarebefordra Azure Automation jobbdata till OMS Log Analytics | Microsoft Docs
description: "Den här artikeln visar hur du skicka jobbstatus och runbook-jobbet strömmar till logganalys för Microsoft Operations Management Suite att ge ytterligare insikter och hantering."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: b3b9457e6c8ce501a7295859923838460e7ab6cc
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Vidarebefordra jobbstatus och jobbet strömmar från Automation till logganalys (OMS)
Automatisering kan skicka runbook jobbet status och jobbstatus strömmar till Microsoft Operations Management Suite (OMS) logganalys-arbetsytan.  Jobbet loggar och dataströmmar för jobbet är synliga i Azure-portalen eller med PowerShell, för enskilda jobb och detta kan du utföra enkla undersökningar. Med Log Analytics kan du nu:

* Skaffa dig insikter om dina Automation-jobb
* Utlösare som en e-post eller en avisering baserat på din runbook jobbets status (till exempel misslyckades eller pausas)
* Skriva avancerade frågor över jobb-strömmar
* Korrelera jobb över Automation-konton
* Visualisera dina jobbhistorik över tid     

## <a name="prerequisites-and-deployment-considerations"></a>Krav och överväganden vid distribution
Om du vill börja skicka Automation-loggar till logganalys, behöver du:

1. November 2016 eller senare versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
2. Logganalys-arbetsytan. Mer information finns i [Kom igång med logganalys](../log-analytics/log-analytics-get-started.md). 
3. Resurs-ID för Azure Automation-konto

Kör följande PowerShell för att hitta resurs-ID för Azure Automation-konto och logganalys-arbetsyta:

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har flera Automation-konton eller arbetsytor i utdata från de föregående kommandona hitta den *namn* måste du konfigurera och kopiera värdet för *ResourceId*.

Om du vill söka efter den *namn* ditt Automation-konto i Azure portal väljer du ditt Automation-konto från den **Automation-konto** och välj **alla inställningar** .  Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Ställa in integration med logganalys
1. På datorn, startar **Windows PowerShell** från den **starta** skärmen.  
2. Kopiera och klistra in följande PowerShell och redigera värdet för den `$workspaceId` och `$automationAccountId`.  För den `-Environment` parameter, giltiga värden är *AzureCloud* eller *AzureUSGovernment* beroende på hur du arbetar i molnmiljön.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

När du har kört skriptet visas poster i logganalys inom 10 minuter efter nya JobLogs eller JobStreams skrivs.

Kör följande fråga om du vill se loggarna i logganalys loggen sökning:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfigurationen
Kontrollera att diagnostik är korrekt inställt på Automation-kontot med följande PowerShell för att bekräfta att ditt Automation-konto är skicka loggar till logganalys-arbetsytan:

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Se till att i utdata:
+ Under *loggar*, värdet för *aktiverad* är *SANT*
+ Värdet för *WorkspaceId* är inställd på ResourceId för logganalys-arbetsytan


## <a name="log-analytics-records"></a>Log Analytics-poster
Diagnostik från Azure Automation skapar två typer av poster i logganalys och märks **typ = AzureDiagnostics**.

### <a name="job-logs"></a>Jobbloggar
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet.  Möjliga värden:<br>-Nya<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Slutförd |
| Kategori | Klassificering av typ av data.  För Automation är värdet JobLogs. |
| OperationName | Anger åtgärdstypen i Azure.  Värdet är jobb för automatisering. |
| Resurs | Namnet på det Automation-kontot |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Beskriver jobbstatusen för runbook.  Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| Resurs-ID |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Dataströmmar för jobbet
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet.  Möjliga värden:<br>-Pågår |
| Kategori | Klassificering av typ av data.  För Automation är värdet JobStreams. |
| OperationName | Anger åtgärdstypen i Azure.  Värdet är jobb för automatisering. |
| Resurs | Namnet på det Automation-kontot |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Innehåller utdataströmmen från runbook. |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| Resurs-ID |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visa Automation loggar i logganalys
Nu när du har startat skicka dina Automation-jobbloggar till logganalys kan vi se vad du kan göra med dessa loggar i logganalys.

Kör följande fråga om du vill visa loggarna:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när en runbook-jobb misslyckas eller pausar
En av våra främsta kunden frågar avser möjlighet att skicka ett e-postmeddelande eller en text om något går fel med ett runbook-jobb.   

Om du vill skapa en aviseringsregel, börja med att skapa en logg Sök efter poster för runbook-jobb som ska anropa aviseringen.  Klicka på den **avisering** för att skapa och konfigurera varningsregeln.

1. Översikt över Log Analytics-sidan klickar du på **loggen Sök**.
2. Skapa en logg sökfråga för aviseringen genom att skriva följande sökningen i fältet fråga: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)` du kan också gruppera efter RunbookName med hjälp av:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Om du har lagt upp loggar från mer än en Automation-konto eller prenumeration på din arbetsyta kan gruppera du aviseringar av prenumeration och Automation-konto.  Automation-kontonamnet kan härledas från fältet resurs i sökningen i JobLogs.  
3. Öppna den **lägga till Varningsregeln** klickar du på **avisering** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns [aviseringar i logganalys](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel
Du hittar förutom Varna vid fel när en runbook-jobbet har en icke-avslutande fel. I dessa fall PowerShell genererar ett fel uppstod när strömmen, men inte avslutar felen orsakar inte ditt jobb att pausa eller misslyckas.    

1. Klicka på i logganalys-arbetsytan **loggen Sök**.
2. Ange i fältet fråga `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` och klicka sedan på **Sök**.

### <a name="view-job-streams-for-a-job"></a>Visa jobb dataströmmar för ett jobb
När du felsöker ett jobb, kan du även vill söka i dataströmmar för jobbet.  Följande fråga visar alla dataströmmar för ett enda projekt med GUID 2ebd22ea e05e-4eb9 - 9d 76-d73cbd4356e0:   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Visa historiska jobbstatus
Slutligen kan du visualisera dina jobbhistorik över tid.  Du kan använda den här frågan för att söka efter status för jobb med tiden.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![OMS historiska jobbet Status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Sammanfattning
Du kan få bättre inblick i ditt Automation-jobb efter status genom att skicka ditt Automation jobbdata status och dataströmmen till logganalys:
+ Ställa in aviseringar för att meddela dig när det uppstår problem
+ Använda anpassade vyer och sökfrågor visualisera dina runbook-resultat, relaterade runbook jobbstatus och andra viktiga indikatorer eller mått.  

Log Analytics ger bättre operativa synlighet till Automation-jobb och kan hjälpa adress incidenter snabbare.  

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du konstruerar olika sökfrågor och granskar jobbloggarna i Automation med Log Analytics i [Logga sökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Information om hur du skapar och hämta utdata och felmeddelanden från runbooks finns [utdata och meddelanden](automation-runbook-output-and-messages.md)
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
* Läs mer om OMS Log Analytics och datakällsamling i [Samla in data om Azure-lagring i Log Analytics-översikten](../log-analytics/log-analytics-azure-storage.md)
