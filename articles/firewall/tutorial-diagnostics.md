---
title: Självstudie – Övervaka Azure Firewall-loggar och mått
description: I den här självstudien får du lära dig att aktivera och hantera Azure Firewall-loggar och mått.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 2befbf66733430e6077f5e5ff3044c30a77b7e5c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958991"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Självstudier: Övervaka Azure Firewall-loggar och mått

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser. Med hjälp av mått kan du visa prestandaräknare i portalen. 

Du kan komma åt vissa av de här loggarna via portalen. Du kan skicka loggar till [Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md), Storage och Event Hubs samt analysera dem i Azure Monitor-loggar eller med andra verktyg såsom Excel och Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * aktivera loggning via Azure Portal
> * aktivera loggning med PowerShell
> * visa och analysera aktivitetsloggar
> * Visa och analysera loggar för nätverk och programregler
> * Visa mått

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudien bör du läsa [Azure Firewall-loggar och mått](logs-and-metrics.md) för att få en översikt över de diagnostikloggar och mått som finns för Azure Firewall.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Aktivera diagnostisk loggning via Azure Portal

Det kan ta några minuter innan data visas i loggarna när du har aktiverat diagnostisk loggning. Om du inte ser någonting direkt kan du kontrollera igen om några minuter.

1. Öppna brandväggens resursgrupp i Azure Portal och klicka på brandväggen.
2. Under **Övervakning** klickar du på **Diagnostikloggar**.

   Azure Firewall har två loggar som är specifika för tjänsten:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Om du vill börja samla in data klickar du på **Aktivera diagnostik**.
4. På sidan **Diagnostikinställningar** kan du göra inställningar för de diagnostiska loggarna. 
5. I det här exemplet lagras loggarna av Azure Monitor-loggar, så använd **Firewall log analytics** som namn.
6. Klicka på **Skicka till Log Analytics** för att konfigurera din arbetsyta. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.
7. Under **Log Analytics** klickar du på **Konfigurera**.
8. På Log Analytics-sidan med arbetsytor klickar du på **Skapa ny arbetsyta**.
9. På sidan **Log Analytics-arbetsyta** anger du **firewall-oms** som namn för den nya **Log Analytics-arbetsytan**.
10. Välj din prenumeration, använd den befintliga resursgruppen för brandväggen (**Test-FW-RG**), välj **USA, östra** som plats och välj prisnivån **Kostnadsfri**.
11. Klicka på **OK**.
   ![Starta konfigurationsprocessen][1] OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.  
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

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure CLI, Azure REST API eller Azure-portalen. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med [Azure Activity Logs-innehållspaketet för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysera dina data på förkonfigurerade instrumentpaneler, som du både kan anpassa och använda i befintligt skick.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Visa och analysera loggar för nätverk och programregler

[Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md) samlar in data från räknaren och händelseloggfilerna. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Log Analytics-exempelfrågor för Azure Firewall finns i [Log Analytics-exempel för Azure Firewall](log-analytics-samples.md).

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="view-metrics"></a>Visa mått
Bläddra till en Azure-brandvägg. Under **Övervakning** klickar du på **Mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat brandväggen för insamling av loggar kan du utforska hur du visar dina data i Azure Monitor-loggar.

> [!div class="nextstepaction"]
> [Lösningar för nätverksövervakning i Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
