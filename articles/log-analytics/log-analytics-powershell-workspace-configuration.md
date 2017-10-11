---
title: "Använda PowerShell för att skapa och konfigurera en Log Analytics-arbetsyta | Microsoft Docs"
description: "Logga Analytics använder data från servrar i din lokala eller molnbaserade infrastrukturen. Du kan samla in Maskindata från Azure storage när genereras av Azure-diagnostik."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6807ab67e3593da82c147669b29bfdae3b6c967c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manage-log-analytics-using-powershell"></a>Hantera Log Analytics med PowerShell
Du kan använda den [Log Analytics PowerShell-cmdlets](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) att utföra olika funktioner i logganalys från en kommandorad eller som en del av ett skript.  Exempel på uppgifter som du kan utföra med PowerShell:

* Skapa en arbetsyta
* Lägg till eller ta bort en lösning
* Importera och exportera sparade sökningar
* Skapa en datorgrupp
* Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
* Samla in prestandaräknare från Linux och Windows-datorer
* Samla in händelser från syslog på Linux-datorer 
* Samla in händelser från händelseloggarna i Windows
* Samla in anpassade händelseloggar
* Lägga till log analytics agenten till en virtuell Azure-dator
* Konfigurera logganalys index data som samlas in med hjälp av Azure-diagnostik

Den här artikeln innehåller två kodexempel som visar några av de funktioner som du kan utföra från PowerShell.  Du kan referera till den [Log Analytics PowerShell cmdlet-referens](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) för andra funktioner.

> [!NOTE]
> Logganalys kallades tidigare åtgärdsinformation, därför är det namn som används i cmdlets.
> 
> 

## <a name="prerequisites"></a>Krav
De här exemplen fungerar med version 2.3.0 eller senare av modulen AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Skapa och konfigurera en Log Analytics-arbetsyta
Följande skriptexempel illustrerar hur du:

1. Skapa en arbetsyta
2. Visa en lista över tillgängliga lösningar
3. Lägg till lösningar på arbetsytan
4. Importera sparade sökningar
5. Exportera sparade sökningar
6. Skapa en datorgrupp
7. Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
8. Samla in logisk Disk prestandaräknarna från Linux-datorer (% noder i procent; Ledigt utrymme i MB; Använt utrymme; i % Disköverföringar/sek; Diskläsningar/sek; Diskskrivningar/sek)
9. Samla in syslog-händelser från Linux-datorer
10. Samla in händelser för fel- och varningsmeddelandena i programmets händelselogg från Windows-datorer
11. Tillgängligt minne i megabyte prestandaräknaren samla in från Windows-datorer
12. Samla in en anpassad logg 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
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
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurera logganalys att indexera Azure-diagnostik
Resurser behöver ha Azure diagnostics aktiverad och konfigurerad för att skriva till logganalys-arbetsytan för övervakning utan Agent för resurser i Azure. Den här metoden skickar data direkt till Log Analytics och kräver inte data skrivs till ett lagringskonto. Resurser som stöds är:

| Resurstyp | Logs | Mått |
| --- | --- | --- |
| Application Gateways    | Ja | Ja |
| Automation-konton     | Ja | |
| Batch-konton          | Ja | Ja |
| Data Lake analytics     | Ja | | 
| Data Lake store         | Ja | |
| Elastisk SQL-poolen        |     | Ja |
| Event Hub namnområde     |     | Ja |
| IoT-hubbar                |     | Ja |
| Key Vault               | Ja | |
| Belastningsutjämnare          | Ja | |
| Logic Apps              | Ja | Ja |
| Nätverkssäkerhetsgrupper | Ja | |
| Redis Cache             |     | Ja |
| Söktjänster         | Ja | Ja |
| Service Bus-namnrymd   |     | Ja |
| SQL (v12)               |     | Ja |
| Webbplatser               |     | Ja |
| Webbservergrupper        |     | Ja |

Mer information om tillgänglig statistik avser [stöds mått med Azure-Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Mer information om de tillgängliga loggarna avser [stöds tjänster och schemat för diagnostikloggar](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Du kan också använda cmdlet föregående för att samla in loggar från resurser som är i olika prenumerationer. Cmdlet kan fungera över prenumerationer eftersom du tillhandahåller id för både den resurs som skapar loggar och loggar som skickas till arbetsytan.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurera logganalys att indexera Azure diagnostics från lagring
Du måste först skriva data till Azure-lagring för att samla in loggdata från en instans av en klassiska molnbaserad tjänst eller ett service fabric-kluster som körs. Logganalys konfigureras sedan för att samla in loggar från lagringskontot. Resurser som stöds är:

* Klassiska molntjänster (webb- och arbetsroller roller)
* Service fabric-kluster

Följande exempel visar hur du:

1. Visa en lista över de befintliga lagringskonton och platser som logganalys indexerar data från
2. Skapa en konfiguration för att läsa från ett lagringskonto
3. Uppdatera konfigurationen för nyskapade att indexera data från fler platser
4. Ta bort den nyligen skapade konfigurationen

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Du kan också använda skriptet för att samla in loggar från lagringskonton för olika prenumerationer. Skriptet kan fungera över prenumerationer eftersom du tillhandahåller lagring konto resurs-id och en motsvarande snabbtangent. När du ändrar snabbtangent som du behöver uppdatera lagring kunskaper om du vill att den nya nyckeln.


## <a name="next-steps"></a>Nästa steg
* [Granska Log Analytics PowerShell-cmdlets](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) för ytterligare information om hur du använder PowerShell konfigurationen för logganalys.

