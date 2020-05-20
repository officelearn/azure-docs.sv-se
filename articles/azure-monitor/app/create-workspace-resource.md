---
title: Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurs | Microsoft Docs
description: Lär dig mer om de steg som krävs för att aktivera de nya Azure Monitor Application Insights arbets ytans baserade resurser.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: e66ae6aa0b9b7e309fbd6fcc3699cb873a266bbe
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647889"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Arbets yta-baserade Application Insights resurser (förhands granskning)

Arbets ytans resurser stöder fullständig integrering mellan Application Insights och Log Analytics. Nu kan du välja att skicka din Application Insights telemetri till en gemensam Log Analytics arbets yta, vilket ger dig fullständig åtkomst till alla funktioner i Log Analytics samtidigt som du behåller program-, infrastruktur-och plattforms loggar på en enda konsol IDE rad plats.

Detta möjliggör även för vanliga rollbaserade Access Control (RBAC) i dina resurser och eliminerar behovet av frågor över flera appar/arbets ytor.

> [!NOTE]
> Data inmatning och kvarhållning för arbets ytans baserade Application Insights resurser faktureras via arbets ytan Log Analytics där data finns. [Läs mer]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) om fakturering för arbets ytans baserade Application Insights-resurser.

Om du vill testa den nya upplevelsen loggar du in på [Azure Portal](https://portal.azure.com)och skapar en Application Insights resurs:

![Arbets yta-baserad Application Insights resurs](./media/create-workspace-resource/create-workspace-based.png)

Om du inte redan har en befintlig Log Analytics arbets yta kan du [läsa dokumentationen för att skapa Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

För den offentliga för hands versions **arbets ytan är resurserna för närvarande begränsade till västra USA 2, östra USA och södra centrala USA.**

När din resurs har skapats visas motsvarande information om arbets ytan i **översikts** fönstret:

![Namn på arbetsyta](./media/create-workspace-resource/workspace-name.png)

Om du klickar på den blå länk texten tas du till den associerade Log Analytics arbets ytan där du kan dra nytta av den nya frågan i enhetlig arbets yta.

> [!NOTE]
> Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resurs frågor, arbets böcker och loggbaserade aviseringar inom Application Insightss upplevelsen. Om du vill fråga/Visa mot den [nya arbetsytebaserade tabell strukturen/schemat](apm-tables.md) måste du först gå till din Log Analytics-arbetsyta. Under förhands granskningen får du till gång till den klassiska Application Insights fråge upplevelsen genom att välja **loggar** från Application Insights fönstret.

## <a name="copy-the-connection-string"></a>Kopiera anslutningssträngen

[Anslutnings strängen](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

## <a name="monitoring-configuration"></a>Övervaknings konfiguration

När en arbets yta-baserad Application Insights resurs har skapats är det relativt enkelt att konfigurera övervakning.

### <a name="code-based-application-monitoring"></a>Kod baserad program övervakning

För kodbaserade program övervakning installerar du bara lämplig Application Insights SDK och pekar på Instrumentation-nyckeln eller anslutnings strängen till den nya resursen.  

Detaljerad dokumentation om hur du konfigurerar en Application Insights SDK för kodbaserade övervakning finns i språk-och Framework-specifik dokumentation:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Bakgrunds aktiviteter & moderna konsol program (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Klassiska konsol program (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Programmeringsspråket](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Övervakning av kod och Visual Studio-resurs skapas

För kod lös övervakning av tjänster som Azure Functions-och Azure App-tjänster måste du också först skapa din arbets yta-baserade Application Insights-resurs och sedan peka på resursen under övervaknings konfigurations fasen.

Dessa tjänster erbjuder alternativet att skapa en ny Application Insights-resurs i en egen resurs skapande process, men resurser som skapats via dessa GRÄNSSNITTs alternativ är för närvarande begränsade till den klassiska Application Insightss upplevelsen.

Samma sak gäller för Application Insights att skapa resurser i Visual Studio för ASP.NET och ASP.NET Core. Du måste välja en befintlig arbets yta baserad resurs från med gränssnittet för Visual Studio Monitoring-aktivering. Om du väljer Skapa ny resurs i Visual Studio begränsas du till att skapa en klassisk Application Insights-resurs.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt

### <a name="azure-cli"></a>Azure CLI

För att få åtkomst till för hands versionen Application Insights Azure CLI-kommandon måste du först köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör `az extension add` kommandot visas ett fel meddelande som säger:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa din Application Insights-resurs:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Fullständig Azure CLI-dokumentation för det här kommandot finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`PowerShell-kommandot har för närvarande inte stöd för att skapa en arbets yta baserad Application Insights resurs. Om du vill skapa en arbets yta baserad resurs med PowerShell kan du använda Azure Resource Manager-mallarna nedan och distribuera med PowerShell.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

#### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameter fil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Ändra den associerade arbets ytan

När en arbets yta-baserad Application Insights resurs har skapats kan du ändra den associerade Log Analytics-arbetsytan.

I fönstret Application Insights resurs väljer du **Egenskaper**  >  **ändra arbets yta**  >  **Log Analytics arbets ytor**

## <a name="export-telemetry"></a>Exportera telemetri

Äldre funktioner för kontinuerlig export stöds inte för arbets ytans resurser. I stället väljer du **diagnostikinställningar**  >  **Lägg till diagnostisk inställning** inifrån din Application Insights-resurs. Du kan välja alla tabeller eller en delmängd av tabeller för att arkivera till ett lagrings konto eller strömma till en Azure Event Hub.

## <a name="next-steps"></a>Nästa steg

* [Utforska mått](../../azure-monitor/platform/metrics-charts.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
