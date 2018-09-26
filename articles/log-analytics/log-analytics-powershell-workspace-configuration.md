---
title: Använd PowerShell för att skapa och konfigurera en Log Analytics-arbetsyta | Microsoft Docs
description: Logga Analytics använder data från servrar i din lokala eller molnbaserade infrastruktur. Du kan samla in Maskindata från Azure storage när genereras av Azure-diagnostik.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: richrund
ms.component: ''
ms.openlocfilehash: f30114797c7ba62fde555487d3202de5edf48ecb
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182356"
---
# <a name="manage-log-analytics-using-powershell"></a>Hantera Log Analytics med PowerShell
Du kan använda den [Log Analytics PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) att utföra olika funktioner i Log Analytics från en kommandorad eller som en del av ett skript.  Exempel på de uppgifter du kan utföra med PowerShell:

* Skapa en arbetsyta
* Lägga till eller ta bort en lösning
* Importera och exportera sparade sökningar
* Skapa en datorgrupp
* Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
* Samla in prestandaräknare från Linux- och Windows-datorer
* Samla in händelser från syslog på Linux-datorer 
* Samla in händelser från Windows-händelseloggar
* Samla in anpassade händelseloggar
* Lägg till log analytics-agenten till en Azure-dator
* Konfigurera log analytics för att indexera data som samlas in med Azure-diagnostik

Den här artikeln innehåller två kodexempel som illustrerar några av de funktioner som du kan utföra från PowerShell.  Du kan referera till den [Log Analytics PowerShell cmdlet-referens](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) för andra funktioner.

> [!NOTE]
> Log Analytics kallades tidigare för Operational Insights, vilket är anledningen till det är det namn som används i cmdlets.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
De här exemplen fungerar med version 2.3.0 eller senare av modulen AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Skapa och konfigurera en Log Analytics-arbetsyta
Följande skriptexempel illustrerar hur du:

1. Skapa en arbetsyta
2. Lista över tillgängliga lösningar
3. Lägga till lösningar i arbetsytan
4. Importera sparade sökningar
5. Exportera sparade sökningar
6. Skapa en datorgrupp
7. Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
8. Samla in prestandaräknare för logisk Disk från Linux-datorer (% noder i procent; Ledigt utrymme i MB; Använt utrymme; i % Disköverföringar/sek; Diskläsningar/sek; Diskskrivningar/sek)
9. Samla in syslog-händelser från Linux-datorer
10. Samla in händelser för fel och varningar från programmets händelselogg från Windows-datorer
11. Samla in prestandaräknaren för minne tillgängligt, MB från Windows-datorer
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

# List enabled solutions
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

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurera Log Analytics för att indexera Azure-diagnostik
För övervakning utan Agent för Azure-resurser, måste resurserna som har Azure-diagnostik aktiverad och konfigurerad för att skriva till en Log Analytics-arbetsyta. Den här metoden skickar data direkt till Log Analytics och kräver inte data som ska skrivas till ett lagringskonto. Resurser som stöds är:

| Resurstyp | Logs | Mått |
| --- | --- | --- |
| Programgateways    | Ja | Ja |
| Automation-konton     | Ja | |
| Batch-konton          | Ja | Ja |
| Data Lake analytics     | Ja | | 
| Data Lake store         | Ja | |
| Elastiska SQL-Pool        |     | Ja |
| Namnområde för händelsehubb     |     | Ja |
| IoT-hubbar                |     | Ja |
| Key Vault               | Ja | |
| Lastbalanserare          | Ja | |
| Logic Apps              | Ja | Ja |
| Nätverkssäkerhetsgrupper | Ja | |
| Redis Cache             |     | Ja |
| Söktjänster         | Ja | Ja |
| Service Bus-namnområde   |     | Ja |
| SQL (v12)               |     | Ja |
| Webbplatser               |     | Ja |
| Server webbgrupper        |     | Ja |

Information för tillgängliga mått i [stöds mått med Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Information om tillgängliga loggar finns i [tjänster och -schemat stöds för diagnostikloggar](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Du kan också använda cmdleten föregående för att samla in loggar från resurser som finns i olika prenumerationer. Cmdlet: en kan fungera i alla prenumerationer, eftersom du tillhandahåller id för både den resurs som skapar loggar och arbetsytan loggarna skickas till.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurera Log Analytics för att indexera Azure Diagnostics-data från storage
Du måste först skriva data till Azure storage för att samla in loggdata från en instans av en klassisk molntjänst eller ett service fabric-kluster som körs. Log Analytics konfigureras sedan för att samla in loggar från lagringskontot. Resurser som stöds är:

* Klassiska cloud services (webb- och worker-roller)
* Service fabric-kluster

I följande exempel visas hur du:

1. Lista över befintliga lagringskonton och platser som Log Analytics indexerar data från
2. Skapa en konfiguration för att läsa från ett lagringskonto
3. Uppdatera nyligen skapade konfigurationen att indexera data från fler platser
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

Du kan också använda det här skriptet för att samla in loggar från lagringskonton i olika prenumerationer. Skriptet kan fungera mellan prenumerationer eftersom du får tillgång till resurs-id för storage-konto och en motsvarande åtkomstnyckel. När du ändrar att snabbtangent som du behöver uppdatera storage insight om du vill att den nya nyckeln.


## <a name="next-steps"></a>Nästa steg
* [Granska Log Analytics PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) för ytterligare information om hur du använder PowerShell för att konfigurera Log Analytics.

