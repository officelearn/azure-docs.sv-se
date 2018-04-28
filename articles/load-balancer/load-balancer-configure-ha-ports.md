---
title: Konfigurera portar för hög tillgänglighet för Azure belastningsutjämnare | Microsoft Docs
description: Lär dig mer om hög tillgänglighet portar för intern trafik på alla portar för belastningsutjämning
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/20178
ms.author: kumud
ms.openlocfilehash: eb305986982432d7a432204e3fae8a1dff6a5d74
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurera portar för hög tillgänglighet för en intern belastningsutjämnare

Den här artikeln innehåller ett exempel på distribution med hög tillgänglighet portar på en intern belastningsutjämnare. Mer information om konfigurationer som är specifika för nätverks-virtuella installationer (NVAs) finns på motsvarande provider-webbplatser.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskrivs Standard belastningsutjämnaren. Läs mer om grundläggande belastningsutjämnaren [belastningsutjämnaren översikt](load-balancer-overview.md).

Bilden visar exempel för distribution som beskrivs i den här artikeln följande konfiguration:

- NVAs distribueras i backend-pool med en intern belastningsutjämnare bakom portkonfiguration för hög tillgänglighet. 
- Användardefinierad väg (UDR) tillämpas på undernätsvägar DMZ all trafik NVAs genom att göra nästa hopp som den interna belastningsutjämnarens virtuella IP-Adressen för att läsa in. 
- Den interna belastningsutjämnaren distribuerar trafik till en aktiv NVAs enligt algoritmen för belastningsutjämnaren.
- En NVA bearbetar trafiken och vidarebefordrar det till det ursprungliga målet i backend-undernät.
- Returnerar sökvägen kan ta samma flöde om motsvarande UDR konfigureras på backend-undernät. 

![Exempel på distribution portar för hög tillgänglighet](./media/load-balancer-configure-ha-ports/haports.png)



## <a name="configure-high-availability-ports"></a>Konfigurera portar för hög tillgänglighet

Ställ in en intern belastningsutjämnare med NVAs i backend-poolen för att konfigurera portar för hög tillgänglighet. Ställ in en motsvarande hälsa avsökningen belastningsutjämningskonfigurationen identifiera NVA hälsa och belastningsutjämningsregeln med hög tillgänglighet portar. Den allmänna belastning rör belastningsutjämning konfigurationen beskrivs i [Kom igång](load-balancer-get-started-ilb-arm-portal.md). Den här artikeln visar portkonfiguration för hög tillgänglighet.

Konfigurationen i praktiken innebär frontend porten och backend-portvärde till **0**. Värdet protokollet **alla**. Den här artikeln beskriver hur du konfigurerar portar för hög tillgänglighet med hjälp av Azure-portalen, PowerShell och Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurera en regel för belastningsutjämnare portar för hög tillgänglighet med Azure-portalen

För att konfigurera portar för hög tillgänglighet med hjälp av Azure portal, Välj den **HA portar** kryssrutan. När du väljer fylls automatiskt relaterade konfigurationen för port och protokoll. 

![Hög tillgänglighet portkonfiguration via Azure portal](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurera en hög tillgänglighet portar belastningsutjämning regel via Resource Manager-mall

Du kan konfigurera portar för hög tillgänglighet med hjälp av API-versionen 2017-08-01 för Microsoft.Network/loadBalancers i resursen belastningsutjämnaren. Följande JSON-fragment visar ändringarna i belastningsutjämningskonfigurationen för hög tillgänglighet portar via REST-API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurera en regel för belastningsutjämnare portar för hög tillgänglighet med PowerShell

Använd följande kommando för att skapa hög tillgänglighet portar belastningsutjämningsregeln när du skapar den interna belastningsutjämnaren med PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Konfigurera en regel för belastningsutjämnare portar för hög tillgänglighet med Azure CLI 2.0

Steg 4 i [skapa en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-cli.md), Använd följande kommando för att skapa hög tillgänglighet portar belastningsutjämningsregeln:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hög tillgänglighet portar](load-balancer-ha-ports-overview.md).
