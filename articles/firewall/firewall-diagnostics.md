---
title: Övervaka Azure Firewall-loggar och mått
description: I den här artikeln får du lära dig hur du aktiverar och hanterar Azure Firewall-loggar och-mått.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2899121db4b6a3f202be4860e2e4f43027cdef7c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348777"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Övervaka Azure Firewall-loggar och mått

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser. Med hjälp av mått kan du visa prestandaräknare i portalen.

Du kan komma åt vissa av de här loggarna via portalen. Loggar kan skickas till [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), lagring och Event Hubs och analyseras i Azure Monitor loggar eller av olika verktyg som Excel och Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du läsa [Azure Firewall-loggar och-mått](logs-and-metrics.md) för en översikt över de diagnostikloggar och mät värden som är tillgängliga för Azure-brandväggen.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Aktivera diagnostisk loggning via Azure Portal

Det kan ta några minuter innan data visas i loggarna när du har aktiverat diagnostisk loggning. Om du inte ser någonting direkt kan du kontrollera igen om några minuter.

1. Öppna brand Väggs resurs gruppen i Azure Portal och välj brand väggen.
2. Under **Övervakning** väljer du **Diagnostikinställningar**.

   Det finns fyra tjänstspecifika loggar för Azure-brand väggen:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Välj **Lägg till diagnostisk inställning**. På sidan **Diagnostikinställningar** kan du göra inställningar för diagnostikloggarna.
5. I det här exemplet lagras loggarna av Azure Monitor-loggar, så använd **Firewall log analytics** som namn.
6. Under **logg** väljer du **AzureFirewallApplicationRule** , **AzureFirewallNetworkRule** , **AzureFirewallThreatIntelLog** och **AzureFirewallDnsProxy** för att samla in loggarna.
7. Konfigurera din arbets yta genom att välja **Skicka till Log Analytics** .
8. Välj din prenumeration.
9. Välj **Spara**.

## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Däremot måste du aktivera diagnostisk loggning om du vill börja samla in de data som är tillgängliga via dessa loggar.

Så här aktiverar du diagnostisk loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet är av formatet: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\>*.

   Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal. Informationen finns på sidan **Egenskaper** för resursen.

2. Anteckna resurs-ID:t för den brandvägg som ska loggas. Det här värdet är av formatet: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\>*.

   Du hittar den här informationen i Azure Portal.

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Du behöver inget separat lagringskonto för diagnostiska loggar. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

## <a name="enable-diagnostic-logging-by-using-azure-cli"></a>Aktivera diagnostisk loggning med hjälp av Azure CLI

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Däremot måste du aktivera diagnostisk loggning om du vill börja samla in de data som är tillgängliga via dessa loggar.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="enable-diagnostic-logging"></a>Aktivera diagnostisk loggning

Använd följande kommandon för att aktivera diagnostisk loggning.

1. Kör kommandot [AZ Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) för att aktivera diagnostisk loggning:

   ```azurecli
   az monitor diagnostic-settings create –name AzureFirewallApplicationRule \
     --resource Firewall07 --storage-account MyStorageAccount
   ```

   Kör kommandot [AZ Monitor Diagnostic-Settings List](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_list) för att Visa diagnostikinställningar för en resurs:

   ```azurecli
   az monitor diagnostic-settings list --resource Firewall07
   ```

   Använd [AZ Monitor Diagnostic-Settings show](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_show) för att visa de aktiva diagnostikinställningar för en resurs:

   ```azurecli
   az monitor diagnostic-settings show --name AzureFirewallApplicationRule --resource Firewall07
   ```

1. Kör kommandot [AZ Monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) för att uppdatera inställningarna.

   ```azurecli
   az monitor diagnostic-settings update --name AzureFirewallApplicationRule --resource Firewall07 --set retentionPolicy.days=365
   ```

   Använd kommandot [AZ Monitor Diagnostic-Settings Delete](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_delete) för att ta bort en diagnostisk inställning.

   ```azurecli
   az monitor diagnostic-settings delete --name AzureFirewallApplicationRule --resource Firewall07
   ```

> [!TIP]
>Du behöver inget separat lagringskonto för diagnostiska loggar. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

## <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg** : Hämta information från aktivitetsloggen via Azure PowerShell, Azure-CLI:t, Azure REST-API:t eller Azure Portal. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI** : Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med [innehållspaketet Azure aktivitetsloggar för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysera dina data på förkonfigurerade instrumentpaneler, som du både kan anpassa och använda i befintligt skick.
* **Azure Sentinel** : du kan ansluta Azure Firewall-loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra din undersökning. Azure Firewall data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Mer information finns i [ansluta data från Azure-brandväggen](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Visa och analysera loggar för nätverk och programregler

[Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md) samlar in data från räknaren och händelseloggfilerna. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Log Analytics-exempelfrågor för Azure Firewall finns i [Log Analytics-exempel för Azure Firewall](log-analytics-samples.md).

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="view-metrics"></a>Visa mått
Bläddra till en Azure-brandvägg, under **övervakning** Välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat brandväggen för insamling av loggar kan du utforska hur du visar dina data i Azure Monitor-loggar.

[Lösningar för nätverksövervakning i Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md)
