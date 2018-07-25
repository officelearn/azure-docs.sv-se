---
title: Självstudie – Övervaka Azure Firewall-loggar
description: I den här självstudien får du lära dig att aktivera och hantera Azure Firewall-loggar.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991963"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Självstudie: Övervaka Azure Firewall-loggar

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Exemplen i Azure Firewall-artikeln förutsätter att du redan har aktiverat den offentliga förhandsversionen av Azure Firewall. Mer information finns i [Aktivera den offentliga förhandsversionen av Azure Firewall](public-preview.md).

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Du kan komma åt vissa av de här loggarna via portalen. Du kan skicka loggar till [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Storage och Event Hubs, och analysera dem i Log Analytics eller med andra verktyg som Excel och Power BI.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * aktivera loggning via Azure Portal
> * aktivera loggning med PowerShell
> * visa och analysera aktivitetsloggar
> * visa och analysera loggar för nätverk och programregler.

## <a name="diagnostic-logs"></a>Diagnostikloggar

 Följande diagnostiska loggar är tillgängliga för Azure Firewall:

* **Programregelloggen**

   Programregelloggen sparas till ett lagringskonto, strömmas till Event Hubs och/eller skickas till Log Analytics om du har aktiverat det för respektive Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade programreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Nätverksregellogg**

   Nätverksregelloggen sparas till ett lagringskonto, strömmas till en händelsehubb och/eller skickas till Log Analytics om du har aktiverat det för respektive Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade nätverksreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Du har tre alternativ för att lagra dina loggar:

* **Storage-konto**: Storage-konton passar bäst när loggarna ska lagras en längre tid och granskas vid behov.
* **Händelsehubbar**: Händelsehubbar är ett bra alternativ vid integrering med andra verktyg för säkerhetsinformation och händelsehantering (SEIM), när du vill få aviseringar om dina resurser.
* **Log Analytics**: Log Analytics passar bäst till allmän realtidsövervakning av ditt program eller när du vill leta efter trender.

## <a name="activity-logs"></a>Aktivitetsloggar

   Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.

   Du kan använda [Azure-aktivitetsloggar](../azure-resource-manager/resource-group-audit.md) (kallades tidigare för driftloggar och granskningsloggar) till att visa alla åtgärder som skickas till din Azure-prenumeration.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Aktivera diagnostisk loggning via Azure Portal

Det kan ta några minuter innan data visas i loggarna när du har aktiverat diagnostisk loggning. Om du inte ser någonting direkt kan du kontrollera igen om några minuter.

1. Öppna brandväggens resursgrupp i Azure Portal och klicka på brandväggen.
2. Under **Övervakning** klickar du på **Diagnostikloggar**.

   Azure Firewall har två loggar som är specifika för tjänsten:

   * Programregellogg
   * Nätverksregellogg

3. Om du vill börja samla in data klickar du på **Aktivera diagnostik**.
4. På sidan **Diagnostikinställningar** kan du göra inställningar för de diagnostiska loggarna. 
5. I det här exemplet lagras loggarna i Log Analytics, så använd **Firewall log analytics** som namn.
6. Klicka på **Skicka till Log Analytics** för att konfigurera din arbetsyta. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.
7. Under **Log Analytics** klickar du på **Konfigurera**.
8. På sidan OMS-arbetsytor klickar du på **Skapa ny arbetsyta**.
9. På sidan **Log Analytics-arbetsyta** anger du **firewall-oms** som namn för den nya **OMS-arbetsytan**.
10. Välj din prenumeration, använd den befintliga resursgruppen för brandväggen (**Test-FW-RG**), välj **USA, östra** som plats och välj prisnivån **Kostnadsfri**.
11. Klicka på **OK**.
   ![Starta konfigurationsprocessen][1]
12. Under **Logg** klickar du på **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule** så att du samlar in loggar för både program- och nätverksregler.
   ![Spara diagnostikinställningar][2]
13. Klicka på **Spara**.

## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Däremot måste du aktivera diagnostisk loggning om du vill börja samla in de data som är tillgängliga via dessa loggar.

Så här aktiverar du diagnostisk loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet har följande format: */subscriptions/\<prenumerations-ID\>/resourceGroups/\<resursgruppsnamn\>/providers/Microsoft.Storage/storageAccounts/\<lagringskontonamn\>*.

   Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal. Informationen finns på sidan **Egenskaper** för resursen.

2. Anteckna resurs-ID:t för den brandvägg som ska loggas. Det här värdet har följande format: */subscriptions/\<prenumerations-ID\>/resourceGroups/\<resursgruppsnamn\>/providers/Microsoft.Network/azureFirewalls/\<brandväggsnamn\>*.

   Du hittar den här informationen i Azure Portal.

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Du behöver inget separat lagringskonto för diagnostiska loggar. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

## <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure-CLI:t, Azure REST-API:t eller Azure Portal. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med [Azure Activity Logs-innehållspaketet för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysera dina data på förkonfigurerade instrumentpaneler, som du både kan anpassa och använda i befintligt skick.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Visa och analysera loggar för nätverk och programregler

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) samlar in data från räknaren och händelseloggfilerna. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.


## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat brandväggen för insamling av loggar kan du utforska hur du kan visa dina data i Log Analytics.

> [!div class="nextstepaction"]
> [Lösningar för nätverksövervakning i Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
