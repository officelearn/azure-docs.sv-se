---
title: Nätverket säkerhetshändelse grupp och regel prestandaräknaren Azure diagnostikloggar
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar händelse och regeln räknaren diagnostikloggar för en säkerhetsgrupp i Azure-nätverk.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: 6cc75a70dfc7022efa8577234cc67659d53b2f88
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194425"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Diagnostisk loggning för en grupp

En nätverkssäkerhetsgrupp (NSG) innehåller regler som tillåter eller nekar trafik till ett virtuellt nätverksundernät, nätverksgränssnitt eller båda. När du aktiverar loggning för en NSG måste logga du följande typer av information:

* **Händelse:** Poster loggas för vilka NSG reglerna tillämpas på virtuella datorer, baserat på MAC-adress. Status för de här reglerna som samlas in var 60: e sekund.
* **Regeln räknare:** Innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik.

Diagnostikloggar är bara tillgängliga för NSG: er som distribueras via Azure Resource Manager-distributionsmodellen. Du kan inte aktivera Diagnostisk loggning för NSG: er som distribueras via den klassiska distributionsmodellen. En bättre förståelse av de två modellerna, finns i [förstå Azure-distributionsmodellerna](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostisk loggning aktiveras separat för *varje* NSG som du vill samla in diagnostikdata för. Om du är intresserad av driftdatabasen, eller aktivitet, loggas i stället kan du läsa Azure [aktivitetsloggning](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Aktivera loggning

Du kan använda den [Azure-portalen](#azure-portal), [PowerShell](#powershell), eller [Azure CLI](#azure-cli) diagnostikloggning ska aktiveras.

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [portalen](https://portal.azure.com).
2. Välj **alla tjänster**, Skriv *nätverkssäkerhetsgrupper*. När **Nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
3. Välj NSG: N som du vill aktivera loggning för.
4. Under **övervakning**väljer **diagnostikloggar**, och välj sedan **slå på diagnostik**, enligt följande bild:

   ![Slå på diagnostik](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Under **diagnostikinställningar**anger, eller Välj följande information och välj sedan **spara**:

    | Inställning                                                                                     | Värde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Namn                                                                                        | Ett namn du väljer.  Till exempel: *myNsgDiagnostics*      |
    | **Arkivera till ett lagringskonto**, **Stream till en händelsehubb**, och **skicka till Log Analytics** | Du kan välja så många mål som du väljer. Mer information om var och en finns [logga mål](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Välj loggkategorier ena eller båda. Mer information om de data som loggats för varje kategori finns [logga kategorier](#log-categories).                                                                                                                                             |
6. Visa och analysera loggar. Mer information finns i [visa och analysera loggar](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn, hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också behöva köra `Connect-AzAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Om du vill aktivera Diagnostisk loggning, behöver du Id för en befintlig Nätverkssäkerhetsgrupp. Om du inte har en befintlig Nätverkssäkerhetsgrupp kan du skapa en med [New AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Hämtar nätverkssäkerhetsgruppen som du vill aktivera Diagnostisk loggning för med [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Till exempel att hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup*, anger du följande kommando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Du kan skriva diagnostikloggar för tre typer av mål. Mer information finns i [logga mål](#log-destinations). I den här artikeln loggarna skickas till den *Log Analytics* mål, som exempel. Hämta en befintlig Log Analytics-arbetsyta med [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Till exempel att hämta en befintlig arbetsyta med namnet *myWorkspace* i en resursgrupp med namnet *myWorkspaces*, anger du följande kommando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Om du inte har en befintlig arbetsyta kan du skapa en med [New AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Det finns två typer av loggning som du kan aktivera loggar för. Mer information finns i [logga kategorier](#log-categories). Aktivera Diagnostisk loggning för NSG med [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). I följande exempel loggar händelse- och räknaren kategoridata till arbetsytan för en NSG med ID: N för NSG och arbetsytan som du hämtade tidigare:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Om du bara vill logga data för en kategori eller den andra, snarare än både lägger du till den `-Categories` möjlighet att föregående kommando, följt av *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du vill logga in till en annan [mål](#log-destinations) än en Log Analytics-arbetsyta, använder du parametrarna för en Azure [lagringskonto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Event Hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visa och analysera loggar. Mer information finns i [visa och analysera loggar](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra Azure CLI från datorn. Azure Cloud Shell är ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör CLI från datorn, måste version 2.0.38 eller senare. Kör `az --version` på datorn, hitta den installerade versionen. Om du behöver uppgradera kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du kör CLI lokalt måste du också behöva köra `az login` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Om du vill aktivera Diagnostisk loggning, behöver du Id för en befintlig Nätverkssäkerhetsgrupp. Om du inte har en befintlig Nätverkssäkerhetsgrupp kan du skapa en med [az network nsg skapa](/cli/azure/network/nsg#az-network-nsg-create).

Hämtar nätverkssäkerhetsgruppen som du vill aktivera Diagnostisk loggning för med [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show). Till exempel att hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup*, anger du följande kommando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Du kan skriva diagnostikloggar för tre typer av mål. Mer information finns i [logga mål](#log-destinations). I den här artikeln loggarna skickas till den *Log Analytics* mål, som exempel. Mer information finns i [logga kategorier](#log-categories).

Aktivera Diagnostisk loggning för NSG med [az monitor diagnostic-settings skapa](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). I följande exempel loggar händelse- och räknaren kategoridata till en befintlig arbetsyta med namnet *myWorkspace*, som finns i en resursgrupp med namnet *myWorkspaces*, och ID för NSG: N som du hämtade tidigare:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Om du inte har en befintlig arbetsyta kan du skapa en med den [Azure-portalen](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Det finns två typer av loggning som du kan aktivera loggar för. 

Ta bort den kategori som du inte vill logga data för i det föregående kommandot om du bara vill logga data i en kategori eller den andra. Om du vill logga in till en annan [mål](#log-destinations) än en Log Analytics-arbetsyta, använder du parametrarna för en Azure [lagringskonto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Event Hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visa och analysera loggar. Mer information finns i [visa och analysera loggar](#view-and-analyze-logs).

## <a name="log-destinations"></a>Log mål

Diagnostics-data kan vara:
- [Skrivs till ett Azure Storage-konto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), för granskning eller manuell granskning. Du kan ange kvarhållningstid (i dagar) med hjälp av resursdiagnostikinställningar.
- [Strömma till en Event hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ska matas in av en tjänst från tredje part eller anpassade analytics-lösning, t.ex Power BI.
- [Skrivs till Azure Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Loggkategorier

JSON-formaterade data skrivs i följande kategorier i loggen:

### <a name="event"></a>Händelse

Händelseloggen innehåller information om vilka NSG-regler tillämpas på virtuella datorer, baserat på MAC-adress. Följande data loggas för varje händelse. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och en MAC-adressen för 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":[PRIORITY-SPECIFIED-IN-RULE],
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Regeln räknare

Regeln räknarloggen innehåller information om varje regel tillämpas på resurser. Följande exempeldata loggas varje gång som ska tillämpas. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och en MAC-adressen för 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Källans IP-adress för kommunikation loggas inte. Du kan aktivera [NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md) för en NSG dock som loggar alla informationen om regeln, samt källans IP-adress som initierade kommunikationen. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Du kan analysera data med den [traffic analytics](../network-watcher/traffic-analytics.md) funktion i Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Läs hur du visar diagnostiklogg data i [översikt över Azure-diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du skickar diagnostikdata:
- **Log Analytics**: Du kan använda den [network security group analytics](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) lösning för bättre information. Lösningen innehåller visualiseringar för NSG-regler som tillåter eller nekar trafik per MAC-adressen för nätverksgränssnittet i en virtuell dator.
- **Azure Storage-konto**: Data skrivs till en pt1h.JSON. Du hittar den:
  - Händelseloggen på följande sökväg: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Logg för räknaren i följande sökväg: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitetsloggning](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), tidigare känd som gransknings- eller driftloggar. Aktivitetsloggning är aktiverat som standard för NSG: er som skapats via antingen Azure-distributionsmodellen. För att avgöra vilka åtgärder har slutförts på NSG: er i aktivitetsloggen, Sök efter poster som innehåller följande resurstyper:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Läs hur du loggar in diagnostisk information, om du vill inkludera IP-källadressen för varje flöde i [NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).