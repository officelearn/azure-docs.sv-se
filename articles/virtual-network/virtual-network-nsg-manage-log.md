---
title: Diagnostikloggning för en nätverkssäkerhetsgrupp
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar diagnostikloggar för händelse- och regelräknare för en azure-nätverkssäkerhetsgrupp.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751178"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Diagnostikloggning för en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp (NSG) innehåller regler som tillåter eller nekar trafik till ett virtuellt nätverksundernät, nätverksgränssnitt eller båda. När du aktiverar diagnostikloggning för en NSG kan du logga följande informationskategorier:

* **Händelse:** Poster loggas för vilka NSG-regler tillämpas på virtuella datorer, baserat på MAC-adress.
* **Regelräknare:** Innehåller poster för hur många gånger varje NSG-regel tillämpas för att neka eller tillåta trafik. Statusen för dessa regler samlas in var 60:e sekund.

Diagnostikloggar är endast tillgängliga för NSG-grupper som distribueras via Distributionsmodellen för Azure Resource Manager. Du kan inte aktivera diagnostikloggning för NSG:er som distribueras via den klassiska distributionsmodellen. För en bättre förståelse av de två modellerna, se [Förstå Azure-distributionsmodeller](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostikloggning är aktiverat separat för *varje* NSG som du vill samla in diagnostikdata för. Om du är intresserad av drift eller aktivitet, loggar i stället, se [Azure-aktivitetsloggning](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Aktivera loggning

Du kan använda [Azure Portal,](#azure-portal) [PowerShell](#powershell)eller [Azure CLI](#azure-cli) för att aktivera diagnostikloggning.

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och skriv sedan *nätverkssäkerhetsgrupper*. När **Nätverkssäkerhetsgrupper** visas i sökresultaten markerar du den.
3. Välj den NSG som du vill aktivera loggning för.
4. Under **ÖVERVAKNING**väljer du **Diagnostikloggar**och väljer sedan **Aktivera diagnostik**, som visas i följande bild:

   ![Slå på diagnostik](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Under **Inställningar för Diagnostik**anger eller väljer du följande information och väljer sedan **Spara:**

    | Inställning                                                                                     | Värde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Namn                                                                                        | Ett namn som du väljer.  Till exempel: *myNsgDiagnostics*      |
    | **Arkivera till ett lagringskonto,** **Strömma till en händelsehubb**och **Skicka till logganalys** | Du kan välja så många destinationer du vill. Mer information om varje finns i [Logga destinationer](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Välj antingen eller båda loggkategorierna. Mer information om de data som loggas för varje kategori finns i [Loggkategorier](#log-categories).                                                                                                                                             |
6. Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Connect-AzAccount` också köra för att logga in på Azure med ett konto som har [de behörigheter](virtual-network-network-interface.md#permissions)som krävs .

För att aktivera diagnostikloggning behöver du ID för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Hämta den nätverkssäkerhetsgrupp som du vill aktivera diagnostikloggning för med [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup*anger du följande kommando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Du kan skriva diagnostikloggar till tre måltyper. Mer information finns i [Logga destinationer](#log-destinations). I den här artikeln skickas loggar till *log analytics-målet* som ett exempel. Hämta en befintlig Log [Analytics-arbetsyta med Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Om du till exempel vill hämta en befintlig arbetsyta med namnet *myWorkspace* i en resursgrupp med namnet *myWorkspaces*anger du följande kommando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Om du inte har en befintlig arbetsyta kan du skapa en med [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Det finns två kategorier av loggning som du kan aktivera loggar för. Mer information finns i [Logga kategorier](#log-categories). Aktivera diagnostikloggning för NSG med [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). I följande exempel loggas både händelse- och räknarkategoridata till arbetsytan för en NSG med hjälp av ID:n för NSG och arbetsytan som du hämtade tidigare:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Om du bara vill logga data för den ena eller `-Categories` den andra kategorin i stället för båda lägger du till alternativet i föregående kommando, följt av *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du vill logga till en annan [destination](#log-destinations) än en Log Analytics-arbetsyta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra Azure CLI från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör CLI från datorn behöver du version 2.0.38 eller senare. Kör `az --version` på datorn för att hitta den installerade versionen. Om du behöver uppgradera läser du [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du kör CLI lokalt måste du `az login` också köra för att logga in på Azure med ett konto som har [de behörigheter](virtual-network-network-interface.md#permissions)som krävs .

För att aktivera diagnostikloggning behöver du ID för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

Hämta den nätverkssäkerhetsgrupp som du vill aktivera diagnostikloggning för med [az-nätverks nsg show](/cli/azure/network/nsg#az-network-nsg-show). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup*anger du följande kommando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Du kan skriva diagnostikloggar till tre måltyper. Mer information finns i [Logga destinationer](#log-destinations). I den här artikeln skickas loggar till *log analytics-målet* som ett exempel. Mer information finns i [Logga kategorier](#log-categories).

Aktivera diagnostikloggning för NSG med [az monitor diagnostik-inställningar skapa](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). I följande exempel loggas både händelse- och räknarkategoridata till en befintlig arbetsyta med namnet *myWorkspace*, som finns i en resursgrupp med namnet *myWorkspaces*och ID:t för NSG som du hämtade tidigare:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Om du inte har en befintlig arbetsyta kan du skapa en med [Azure-portalen](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Det finns två kategorier av loggning som du kan aktivera loggar för.

Om du bara vill logga data för den ena eller den andra kategorin tar du bort den kategori som du inte vill logga data för i föregående kommando. Om du vill logga till en annan [destination](#log-destinations) än en Log Analytics-arbetsyta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

## <a name="log-destinations"></a>Logga destinationer

Diagnostikdata kan vara:
- [Skrivet till ett Azure Storage-konto](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), för granskning eller manuell inspektion. Du kan ange kvarhållningstiden (i dagar) med hjälp av resursdiagnostikinställningar.
- [Strömmad till en händelsehubb](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för inmatning av en tjänst från tredje part eller anpassad analyslösning, till exempel PowerBI.
- [Skrivet till Azure Monitor-loggar](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Logga kategorier

JSON-formaterade data skrivs för följande loggkategorier:

### <a name="event"></a>Händelse

Händelseloggen innehåller information om vilka NSG-regler som tillämpas på virtuella datorer, baserat på MAC-adress. Följande data loggas för varje händelse. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och en MAC-adress på 00-0D-3A-92-6A-7C:

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
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
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

### <a name="rule-counter"></a>Regelräknare

Regelräknarloggen innehåller information om varje regel som tillämpas på resurser. Följande exempeldata loggas varje gång en regel tillämpas. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och en MAC-adress på 00-0D-3A-92-6A-7C:

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
> Käll-IP-adressen för kommunikationen loggas inte. Du kan aktivera [NSG-flödesloggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md) för en NSG dock, som loggar alla regelräknare information, samt källan IP-adress som initierade kommunikationen. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Du kan analysera data med [trafikanalysfunktionen](../network-watcher/traffic-analytics.md) i Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Mer information om hur du visar diagnostikloggdata finns i [översikt över Azure Diagnostic Logs](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du skickar diagnostikdata till:
- **Azure Monitor loggar:** Du kan använda [nätverkssäkerhetsgruppanalyslösningen](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) för förbättrade insikter. Lösningen tillhandahåller visualiseringar för NSG-regler som tillåter eller nekar trafik, per MAC-adress, av nätverksgränssnittet på en virtuell dator.
- **Azure Storage-konto**: Data skrivs till en PT1H.json-fil. Du hittar:
  - Händelselogg i följande sökväg:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Regelräknare loggar in i följande sökväg:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitetsloggning](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), tidigare känd som gransknings- eller driftloggar. Aktivitetsloggning är aktiverat som standard för NSG:er som skapats via antingen Azure-distributionsmodell. Om du vill ta reda på vilka åtgärder som slutfördes på NSG:er i aktivitetsloggen letar du efter transaktioner som innehåller följande resurstyper:
  - Microsoft.ClassicNetwork/networkSecurityGroups Microsoft.ClassicNetwork/networkSecurityGroups Microsoft.ClassicNetwork/networkSecurityGroups Microsoft.
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Mer information om hur du loggar diagnostikinformation, om du vill inkludera käll-IP-adressen för varje flöde, finns i [NSG-flödesloggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
