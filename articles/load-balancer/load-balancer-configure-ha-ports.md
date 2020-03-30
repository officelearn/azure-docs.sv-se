---
title: Konfigurera portar med hög tillgänglighet för Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Lär dig hur du använder portar med hög tillgänglighet för belastningsutjämning intern trafik på alla portar
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477790"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurera portar med hög tillgänglighet för en intern belastningsutjämnare

Den här artikeln innehåller ett exempel på distribution av portar med hög tillgänglighet på en intern belastningsutjämnare. Mer information om konfigurationer som är specifika för virtuella nätverksinstallationer finns på motsvarande leverantörswebbplatser.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. I den här artikeln beskrivs Standard belastningsutjämning. Mer information om Grundläggande belastningsutjämnare finns i [Översikt över belastningsutjämnare](load-balancer-overview.md).

Bilden visar följande konfiguration av distributionsexempelet som beskrivs i den här artikeln:

- NVA:erna distribueras i backend-poolen för en intern belastningsutjämnare bakom konfigurationen för portar med hög tillgänglighet. 
- Den användardefinierade vägen (UDR) som tillämpas på DMZ-undernätet dirigerar all trafik till NVA:erna genom att göra nästa hopp som virtuell IP för intern belastningsutjämnare. 
- Den interna belastningsutjämnaren distribuerar trafiken till en av de aktiva NVA:erna enligt belastningsutjämnaralgoritmen.
- NVA bearbetar trafiken och vidarebefordrar den till det ursprungliga målet i backend-undernätet.
- Retursökvägen kan ta samma väg om en motsvarande UDR har konfigurerats i backend-undernätet. 

![Exempeldistribution av portar med hög tillgänglighet](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurera portar med hög tillgänglighet

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera en intern belastningsutjämnare med NVA:erna i backend-poolen om du vill konfigurera portar med hög tillgänglighet. Konfigurera en motsvarande belastningsutjämnad hälsoavsökningskonfiguration för att identifiera NVA-hälsotillstånd och belastningsutjämnarregeln med portar med hög tillgänglighet. Den allmänna belastningsutjämnad konfigurationen beskrivs i [Kom igång](load-balancer-get-started-ilb-arm-portal.md). I den här artikeln beskrivs konfigurationen för portar med hög tillgänglighet.

Konfigurationen innebär i huvudsak att ställa in frontend-porten och backend-portvärdet till **0**. Ange protokollvärdet till **Alla**. I den här artikeln beskrivs hur du konfigurerar portar med hög tillgänglighet med hjälp av Azure-portalen, PowerShell och Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurera en belastningsutjämnadsregel för portar med hög tillgänglighet med Azure-portalen

Om du vill konfigurera portar med hög tillgänglighet med hjälp av Azure-portalen markerar du kryssrutan **HA-portar.** När det här alternativet är markerat fylls den relaterade port- och protokollkonfigurationen i automatiskt. 

![Konfiguration av portar med hög tillgänglighet via Azure-portalen](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurera en belastningsutjämningsregel för hög tillgänglighetsporter via resource manager-mallen

Du kan konfigurera portar med hög tillgänglighet med hjälp av API-versionen 2017-08-01 för Microsoft.Network/loadBalancers i load balancer-resursen. Följande JSON-kodavsnitt illustrerar ändringarna i belastningsutjämnarkonfigurationen för portar med hög tillgänglighet via REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurera en belastningsutjämnad regel för hög tillgänglighet portar med PowerShell

Använd följande kommando för att skapa belastningsutjämnarregeln Hög tillgänglighetsportar medan du skapar den interna belastningsutjämnaren med PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurera en belastningsutjämnadsregel för portar med hög tillgänglighet portar med Azure CLI

I steg 4 [i Skapa en intern belastningsutjämnad uppsättning](load-balancer-get-started-ilb-arm-cli.md)använder du följande kommando för att skapa belastningsutjämnarregeln För hög tillgänglighetsportar:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).