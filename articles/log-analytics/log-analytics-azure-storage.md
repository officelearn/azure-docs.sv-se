---
title: Samla in Azure-tjänstloggar och mått för Log Analytics | Microsoft Docs
description: Konfigurera diagnostik på Azure-resurser att skriva loggar och mått till logganalys.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e8c930acd12b5197238be48722947dac5380be6
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751419"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Samla in Azure-tjänstloggar och mått för användning i logganalys

Det finns fyra olika sätt för att samla in loggar och mått för Azure-tjänster:

1. Azure diagnostics direkt till logganalys (*diagnostik* i följande tabell)
2. Azure diagnostics till Azure-lagring till logganalys (*lagring* i följande tabell)
3. Kopplingar för Azure-tjänster (*kopplingar* i följande tabell)
4. Skript för att samla in och sedan skicka data till logganalys (tomma celler i tabellen nedan och för tjänster som inte anges)


| Tjänst                 | Resurstyp                           | Logs        | Mått     | Lösning |
| --- | --- | --- | --- | --- |
| Programgateways    | Microsoft.Network/applicationGateways   | Diagnostik | Diagnostik | [Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Koppling   | Koppling   | [Application Insights Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (förhandsgranskning) |
| Automation-konton     | Microsoft.Automation/AutomationAccounts | Diagnostik |             | [Mer information](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-konton          | Microsoft.Batch/batchAccounts           | Diagnostik | Diagnostik | |
| Klassiska molntjänster  |                                         | Storage     |             | [Mer information](log-analytics-azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | Diagnostik | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostik |             | |
| Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostik |             | |
| Namnområde för händelsehubb     | Microsoft.EventHub/namespaces           | Diagnostik | Diagnostik | |
| IoT-hubbar                | Microsoft.Devices/IotHubs               |             | Diagnostik | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostik |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Belastningsutjämning          | Microsoft.Network/loadBalancers         | Diagnostik |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostik | Diagnostik | |
| Nätverkssäkerhetsgrupper | Microsoft.Network/networksecuritygroups | Diagnostik |             | [Azure Nätverkssäkerhetsgruppen Analytics](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Recovery-valv         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (förhandsgranskning)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Söktjänster         | Microsoft.Search/searchServices         | Diagnostik | Diagnostik | |
| Service Bus-namnområde   | Microsoft.ServiceBus/namespaces         | Diagnostik | Diagnostik | [Service Bus Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric Analytics (förhandsgranskning)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostik | [Azure SQL Analytics (förhandsgranskning)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | Skript      | [Azure Storage Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuella datorer        | Microsoft.Compute/virtualMachines       | Anknytning   | Anknytning <br> Diagnostik  | |
| Skalningsuppsättningar i virtuella datorer | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostik | |
| Webbservergrupper        | Microsoft.Web/serverfarms               |             | Diagnostik | |
| Webbplatser               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostik | [Azure Web Apps Analytics (förhandsgranskning)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> För att övervaka virtuella Azure-datorer (både Linux och Windows), vi rekommenderar att du installerar den [Log Analytics VM-tillägget](log-analytics-azure-vm-extension.md). Agenten ger information som samlas in från virtuella datorer. Du kan också använda tillägget för virtuella datorer.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure diagnostics direkt till Log Analytics
Många Azure-resurser ska kunna skriva diagnostikloggar och mått direkt till Log Analytics och detta är det bästa sättet för att samla in data för analys. När du använder Azure-diagnostik, skrivs data direkt till Log Analytics och behöver inte först skriva data till lagring.

Azure-resurser som stöder [Azure övervakaren](../monitoring-and-diagnostics/monitoring-overview.md) kan skicka sin loggar och mått direkt till Log Analytics.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden till Log Analytics via diagnostikinställningarna. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. Men när exporteras via diagnostikinställningar mått representeras som alla inkommande meddelanden i alla köer i hubben.
>
>

* Mer information om tillgänglig statistik avser [stöds mått med Azure-Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Mer information om de tillgängliga loggarna avser [stöds tjänster och schemat för diagnostikloggar](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Aktivera diagnostik med PowerShell
Du behöver November 2016 (v2.3.0) eller senare versionen av [Azure PowerShell](/powershell/azure/overview).

Följande PowerShell-exempel visar hur du använder [Set AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) att aktivera diagnostik på en nätverkssäkerhetsgrupp. Samma metod som fungerar för alla resurser som stöds – ange `$resourceId` till resurs-id för den resurs som du vill aktivera diagnostik för.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Aktivera diagnostik med Resource Manager-mallar

Aktivera diagnostik för en resurs när den har skapats och har diagnostiken skickas till logganalys-arbetsytan du kan använda en mall som liknar den nedan. Det här exemplet är för ett Automation-konto men fungerar för alla resurstyper som stöds.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure-diagnostik för lagring och sedan till logganalys

För att samla in loggar från inom vissa resurser, är det möjligt att skicka dem till Azure-lagring och sedan konfigurera logganalys om du vill läsa loggfilerna från lagringsplatsen.

Log Analytics kan använda den här metoden för att samla in diagnostik från Azure storage för följande resurser och loggar:

| Resurs | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Arbetsloggen <br> Tillförlitliga aktören händelse <br> Tillförlitlig tjänst-händelse |
| Virtuella datorer |Linux Syslog <br> Windows-händelse <br> IIS-logg <br> Windows ETWEvent |
| Webbroller <br> Worker-roller |Linux Syslog <br> Windows-händelse <br> IIS-logg <br> Windows ETWEvent |

> [!NOTE]
> Du debiteras normala Azure datatrafikavgifter för lagring och transaktioner när du skickar diagnostik till ett lagringskonto och när logganalys läser data från ditt lagringskonto.
>
>

Se [använda blob storage för IIS- och lagring för händelser](log-analytics-azure-storage-iis-table.md) lära dig mer om hur logganalys samlar in dessa loggar.

## <a name="connectors-for-azure-services"></a>Kopplingar för Azure-tjänster

Det finns en koppling för Application Insights, vilket gör att data som samlas in av Application Insights som ska skickas till logganalys.

Lär dig mer om den [Application Insights connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Skript för att samla in och skicka data till logganalys

Du kan använda ett Azure Automation-skript för Azure-tjänster som inte tillhandahåller ett enkelt sätt att skicka loggar och mått till logganalys för att samla in den loggen och mått. Skriptet kan skicka data till Log Analytics med hjälp av den [datainsamlaren API](log-analytics-data-collector-api.md)

Azure-mallgalleriet har [exempel på användning av Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) att samla in data från tjänster och skicka det till logganalys.

## <a name="next-steps"></a>Nästa steg

* [Använda blob storage för IIS- och lagring för händelser](log-analytics-azure-storage-iis-table.md) att läsa loggarna för Azure services att skriva diagnostik till tabellagring eller IIS-loggar som skrivs till blob storage.
* [Aktivera lösningar](log-analytics-add-solutions.md) att ge insikt om data.
* [Använd sökfrågor](log-analytics-log-searches.md) att analysera data.
