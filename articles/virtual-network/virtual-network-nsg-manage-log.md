---
title: Nätverket säkerhetshändelse grupp och regeln prestandaräknaren Azure diagnostikloggar | Microsoft Docs
description: Lär dig hur du aktiverar händelsen och regeln räknaren diagnostikloggar för en säkerhetsgrupp i Azure-nätverk.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757067"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Diagnostikloggning för en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp (NSG) innehåller regler som tillåter eller nekar trafik till ett undernät för virtuellt nätverk, nätverksgränssnittet eller båda. När du aktiverar loggning för en NSG loggar du följande typer av information:

* **Händelse:** poster loggas för vilka NSG regler tillämpas för virtuella datorer, baserat på MAC-adress. Status för de här reglerna samlas var 60: e sekund.
* **Regelräknare:** innehåller poster för hur många gånger varje NSG-regel används för att neka eller Tillåt trafiken.

Diagnostikloggar är bara tillgängliga för NSG: er som distribueras via Azure Resource Manager-distributionsmodellen. Du kan inte aktivera diagnostikloggning för NSG: er som distribueras via den klassiska distributionsmodellen. För bättre förståelse av de två modellerna, se [förstå Azure distributionsmodeller](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostikloggning aktiveras separat för *varje* NSG som du vill samla in diagnostikdata för. Om du är intresserad fungerar, eller aktivitet, loggas i stället, se Azure [aktivitetsloggning](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Aktivera loggning

Du kan använda den [Azure Portal](#azure-portal), eller [PowerShell](#powershell), för att aktivera diagnostikloggning.

### <a name="azure-portal"></a>Azure Portal

1. Logga in på den [portal](https://portal.azure.com).
2. Välj **alla tjänster**, Skriv *nätverkssäkerhetsgrupper*. När **Nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
3. Välj NSG: N som du vill aktivera loggning för.
4. Under **övervakning**väljer **diagnostik loggar**, och välj sedan **aktivera diagnostiken**som visas i följande bild:
 
    ![Slå på diagnostik](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Under **diagnostikinställningarna**anger, eller Välj följande information och välj sedan **spara**:

    | Inställning                                                                                     | Värde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Namn                                                                                        | Ett namn du väljer.  Till exempel: *myNsgDiagnostics*      |
    | **Arkivet till ett lagringskonto**, **dataströmmen till en händelsehubb**, och **skicka till logganalys** | Du kan markera så många mål som du väljer. Mer information om varje finns [logga mål](#log-destinations).                                                                                                                                           |
    | LOGG                                                                                         | Välj antingen eller båda loggen kategorier. Mer information om de data som loggats för varje kategori finns [logga kategorier](#log-categories).                                                                                                                                             |
6. Visa och analysera loggfiler. Mer information finns i [visa och analysera loggar](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Du kan köra kommandon som visas i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modul, version 6.1.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, måste du också köra `Login-AzureRmAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions)].

Om du vill aktivera diagnostikloggning måste Id för en befintlig NSG. Om du inte har en befintlig NSG, kan du skapa en med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Hämtar nätverkssäkerhetsgruppen som du vill aktivera loggning för med [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Om du till exempel att hämta en NSG namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup*, anger du följande kommando:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Du kan skriva diagnostiska loggar till tre destinationstypen. Mer information finns i [logga mål](#log-destinations). I den här artikeln loggar skickas till den *logganalys* mål, t.ex. Hämta en befintlig logganalys-arbetsyta med [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Om du till exempel att hämta en befintlig arbetsyta namnet *myLaWorkspace* i en resursgrupp med namnet *LaWorkspaces*, anger du följande kommando:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Om du inte har en befintlig arbetsyta kan du skapa en med [ny AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Det finns två typer av loggning kan du aktivera loggarna. Mer information finns i [logga kategorier](#log-categories). Aktivera diagnostikloggning för NSG: N med [Set AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). I följande exempel loggar händelse- och räknare kategoridata till arbetsytan för en NSG med ID: N för NSG och arbetsytan som du hämtade tidigare:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Om du bara vill logga data i en kategori eller den andra i stället både lägga till den `-Categories` alternativet för att det föregående kommandot följt av *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du vill logga in till en annan [mål](#log-destinations) än logganalys-arbetsytan använder lämpliga parametrar för en Azure [lagringskonto](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Händelsehubb](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visa och analysera loggfiler. Mer information finns i [visa och analysera loggar](#view-and-analyze-logs).

## <a name="log-destinations"></a>Loggen mål

Diagnostikdata kan vara:
- [Skrivs till ett Azure Storage-konto](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), för granskning eller Manuell kontroll. Du kan ange kvarhållningstiden (i dagar) med hjälp av diagnostikinställningar för resursen.
- [Strömmas till en händelsehubb](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för införandet av en tjänst från tredje part eller anpassade analytics lösning, t.ex PowerBI.
- [Skrivs till Azure logganalys](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Loggkategorier

JSON-formaterade data skrivs i följande kategorier i loggen:

### <a name="event"></a>Händelse

Händelseloggen innehåller information om vilka NSG-regler tillämpas på virtuella datorer, baserat på MAC-adress. Följande exempeldata loggas för varje händelse:

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
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Regelräknare

Regeln räknarloggen innehåller information om varje regel tillämpas på resurser. Följande exempeldata loggas varje gång som ska tillämpas:

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
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Källans IP-adress för kommunikation är inte inloggad. Du kan aktivera [NSG flödet loggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md) för en NSG dock som loggar alla räknarinformationen regeln, samt källans IP-adress som initierade kommunikationen. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Du kan analysera data med den [trafik analytics](../network-watcher/traffic-analytics.md) möjligheterna för Azure Nätverksbevakaren.

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Information om hur du visar diagnostiska loggdata finns [översikt över Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du skickar diagnostikdata till:
- **Logga Analytics**: du kan använda den [network security group analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) lösning för bättre insikter. Lösningen innehåller visualiseringar för NSG-regler som tillåter eller nekar trafik per MAC-adressen för nätverksgränssnittet i en virtuell dator.
- **Azure Storage-konto**: Data skrivs till en PT1H.json-fil. Du hittar den:
    - Händelselogg på följande sökväg: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Logg för räknaren på följande sökväg: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [aktivitetsloggning](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), tidigare känt som granskningsläge eller operativa loggar. Aktivitetsloggning är aktiverat som standard för NSG: er som skapats via antingen Azure distributionsmodell. För att avgöra vilka åtgärder har slutförts på NSG: er i aktivitetsloggen, leta efter poster som innehåller följande resurstyper:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Mer information om hur du loggar diagnostisk information om du vill inkludera källans IP-adress för varje flöde finns [NSG flödet loggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).