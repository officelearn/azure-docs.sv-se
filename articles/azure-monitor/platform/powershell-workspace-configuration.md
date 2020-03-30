---
title: Skapa & konfigurera logganalys med PowerShell
description: Log Analytics-arbetsytor i Azure Monitor lagrar data från servrar i din lokala infrastruktur eller molninfrastruktur. Du kan samla in datordata från Azure-lagring när de genereras av Azure-diagnostik.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054700"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Hantera log analytics-arbetsyta i Azure Monitor med PowerShell

Du kan använda [Logg Analytics PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights/) för att utföra olika funktioner på en Log Analytics-arbetsyta i Azure Monitor från en kommandorad eller som en del av ett skript.  Exempel på de uppgifter du kan utföra med PowerShell är:

* Skapa en arbetsyta
* Lägga till eller ta bort en lösning
* Importera och exportera sparade sökningar
* Skapa en datorgrupp
* Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
* Samla in prestandaräknare från Linux- och Windows-datorer
* Samla in händelser från syslog på Linux-datorer
* Samla in händelser från Windows-händelseloggar
* Samla in anpassade händelseloggar
* Lägga till logganalysagenten på en virtuell Azure-dator
* Konfigurera logganalys för att indexera data som samlats in med Azure-diagnostik

Den här artikeln innehåller två kodexempel som illustrerar några av de funktioner som du kan utföra från PowerShell.  Du kan referera till [logganalysens PowerShell-cmdletreferens](https://docs.microsoft.com/powershell/module/az.operationalinsights/) för andra funktioner.

> [!NOTE]
> Log Analytics kallades tidigare Operational Insights, vilket är anledningen till att det är namnet som används i cmdlets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Dessa exempel fungerar med version 1.0.0 eller senare av modulen Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Skapa och konfigurera en logganalysarbetsyta
Följande skriptexempel illustrerar hur du:

1. Skapa en arbetsyta
2. Lista tillgängliga lösningar
3. Lägga till lösningar på arbetsytan
4. Importera sparade sökningar
5. Exportera sparade sökningar
6. Skapa en datorgrupp
7. Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
8. Samla in räknare för logisk disk perf från Linux-datorer (% Begagnade innoder; Gratis Megabyte; Använt utrymme i %. Disköverföringar/sek; Diskläsningar/sek; Diskskrivningar per sekund)
9. Samla syslog-händelser från Linux-datorer
10. Samla in fel- och varningshändelser från programhändelseloggen från Windows-datorer
11. Prestandaräknare för samla in minnes tillgängliga Mbytes från Windows-datorer
12. Samla in en anpassad logg

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> Formatet för parametern **CustomLogRawJson** som definierar konfigurationen för en anpassad logg kan vara komplext. Använd [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) för att hämta konfigurationen för en befintlig anpassad logg. Egenskapen **Properties** är den konfiguration som krävs för parametern **CustomLogRawJson.**

I exemplet ovan definierades regexDelimiter som "n"\\för nyrad. Loggavgränsaren kan också vara en tidsstämpel.  Dessa är de format som stöds:

| Format | Json RegEx-formatet \\ använder två för varje \ i en vanlig \\ RegEx så om testning i en RegEx-app minskar till \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> två utrymmen efter MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> där + är + eller a - <br> där zzzz tid offset | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T är en bokstav T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurera Logganalys för att skicka Azure-diagnostik
För agentless övervakning av Azure-resurser måste resurserna ha Azure-diagnostik aktiverat och konfigurerat för att skriva till en Log Analytics-arbetsyta. Den här metoden skickar data direkt till arbetsytan och kräver inte att data skrivs till ett lagringskonto. Resurser som stöds omfattar:

| Resurstyp | Loggar | Mått |
| --- | --- | --- |
| Programgateways    | Ja | Ja |
| Automation-konton     | Ja | |
| Batch-konton          | Ja | Ja |
| Analys av datasjö     | Ja | |
| Data Lake store         | Ja | |
| Elastisk SQL-pool        |     | Ja |
| Namnområde för händelsehubb     |     | Ja |
| IoT-hubbar                |     | Ja |
| Key Vault               | Ja | |
| Lastbalanserare          | Ja | |
| Logic Apps              | Ja | Ja |
| Nätverkssäkerhetsgrupper | Ja | |
| Azure Cache for Redis             |     | Ja |
| Söktjänster         | Ja | Ja |
| Service Bus namnområde   |     | Ja |
| SQL (v12)               |     | Ja |
| Webbplatser               |     | Ja |
| Webbservergrupper        |     | Ja |

Mer information om tillgängliga mått finns [i mått som stöds med Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Mer information om tillgängliga loggar finns [i tjänster som stöds och schema för resursloggar](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Du kan också använda den föregående cmdleten för att samla in loggar från resurser som finns i olika prenumerationer. Cmdlet kan arbeta över prenumerationer eftersom du tillhandahåller ID för både resursskapande loggar och arbetsytan loggarna skickas till.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurera Log Analytics-arbetsyta för att samla in Azure-diagnostik från lagring
Om du vill samla in loggdata från en instans som körs av en klassisk molntjänst eller ett tjänstinfrastrukturkluster måste du först skriva data till Azure-lagring. En Log Analytics-arbetsyta konfigureras sedan för att samla in loggarna från lagringskontot. Resurser som stöds omfattar:

* Klassiska molntjänster (webb- och arbetsroller)
* Kluster för servicevävnad

I följande exempel visas hur du:

1. Lista befintliga lagringskonton och platser som arbetsytan ska indexera data från
2. Skapa en konfiguration som ska läsas från ett lagringskonto
3. Uppdatera den nyskapade konfigurationen för att indexera data från ytterligare platser
4. Ta bort den nyskapade konfigurationen

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Du kan också använda föregående skript för att samla in loggar från lagringskonton i olika prenumerationer. Skriptet kan arbeta över prenumerationer eftersom du tillhandahåller lagringskontoresurs-ID och en motsvarande åtkomstnyckel. När du ändrar åtkomstnyckeln måste du uppdatera lagringsinsikten för att få den nya nyckeln.


## <a name="next-steps"></a>Nästa steg
* [Granska Logg Analytics PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights/) för ytterligare information om hur du använder PowerShell för konfiguration av Logganalys.

