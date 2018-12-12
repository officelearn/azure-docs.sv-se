---
title: Konfigurera portar med hög tillgänglighet för Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Lär dig hur du använder portar med hög tillgänglighet för intern trafik på alla portar för belastningsutjämning
services: load-balancer
documentationcenter: na
author: rdhillon
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: d104fd8a88a55af75faa2a0958733442c083451c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084574"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurera portar med hög tillgänglighet för en intern belastningsutjämnare

Den här artikeln innehåller ett exempel på distribution av portar med hög tillgänglighet på en intern belastningsutjämnare. Mer information om konfigurationer som är specifika för virtuella nätverksinstallationer (Nva) finns på motsvarande provider-webbplatser.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskriver Standard Load Balancer. Läs mer om grundläggande belastningsutjämnare [översikt över Load Balancer](load-balancer-overview.md).

Bilden visar följande konfiguration för distribution till exempel i den här artikeln:

- Nva: erna har distribuerats i backend-poolen med en intern belastningsutjämnare bakom konfigurationen portar med hög tillgänglighet. 
- Den användardefinierade vägen (UDR) tillämpats på undernätvägar DMZ all trafik till nva-enheterna genom att göra nästa hopp som den interna läsa in belastningsutjämnare virtuell IP-adress. 
- Den interna belastningsutjämnaren distribuerar trafik till en aktiv nva: erna enligt algoritmen load balancer.
- NVA behandlar trafiken och vidarebefordrar det till det ursprungliga målet i backend-undernät.
- Den returnera sökvägen kan ta samma väg om en motsvarande UDR konfigureras på backend-undernät. 

![Exempel på distribution portar med hög tillgänglighet](./media/load-balancer-configure-ha-ports/haports.png)



## <a name="configure-high-availability-ports"></a>Konfigurera portar med hög tillgänglighet

Konfigurera portar med hög tillgänglighet genom att ställa in en intern belastningsutjämnare med nva: erna i backend-poolen. Konfigurera en motsvarande hälsotillstånd avsökningen belastningsutjämningskonfigurationen att identifiera NVA hälso- och belastningsutjämningsregel med portar med hög tillgänglighet. Den allmänna belastningsutjämningen konfigurationen beskrivs i [börjar](load-balancer-get-started-ilb-arm-portal.md). Den här artikeln visar hur portar med hög tillgänglighet.

Konfigurationen innebär i stort sett frontend-porten och backend-Portvärdet till **0**. Ange Protokollvärdet **alla**. Den här artikeln beskriver hur du konfigurerar portar med hög tillgänglighet med hjälp av de Azure-portalen, PowerShell och Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurera en belastningsutjämningsregel för portar med hög tillgänglighet med Azure portal

Om du vill konfigurera portar med hög tillgänglighet med hjälp av Azure portal, Välj den **HA Ports** markerar du kryssrutan. När du väljer fylls den relaterade port och protokoll-konfigurationen i automatiskt. 

![Konfiguration för portar med hög tillgänglighet via Azure portal](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurera en portar med hög tillgänglighet regel belastningsutjämning via Resource Manager-mall

Du kan konfigurera portar med hög tillgänglighet med hjälp av API-version 2017-08-01 för Microsoft.Network/loadBalancers i Load Balancer-resursen. Följande JSON-kodfragmentet visar ändringarna i belastningsutjämningskonfigurationen för portar med hög tillgänglighet via REST API:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurera en belastningsutjämningsregel för portar med hög tillgänglighet med PowerShell

Använd följande kommando för att skapa belastningsutjämningsregel för portar med hög tillgänglighet när du skapar den interna belastningsutjämnaren med PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurera en belastningsutjämningsregel för portar med hög tillgänglighet med Azure CLI

Steg 4 i [skapa en intern belastningsutjämningsuppsättning](load-balancer-get-started-ilb-arm-cli.md), Använd följande kommando för att skapa belastningsutjämningsregel för portar med hög tillgänglighet:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).
