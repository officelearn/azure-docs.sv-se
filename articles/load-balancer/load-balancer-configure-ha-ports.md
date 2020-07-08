---
title: Konfigurera portar med hög tillgänglighet för Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Lär dig hur du använder portar med hög tillgänglighet för belastnings utjämning intern trafik på alla portar
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: a71a01629f334534d8eb26847a8f3400efbbeafe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807829"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurera portar med hög tillgänglighet för en intern belastningsutjämnare

Den här artikeln innehåller en exempel distribution av portar med hög tillgänglighet på en intern belastningsutjämnare. Mer information om konfigurationer som är speciella för virtuella nätverks installationer (NVA) finns på motsvarande Provider-webbplatser.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. I den här artikeln beskrivs Standard Load Balancer. Mer information om grundläggande Load Balancer finns i [Load Balancer översikt](load-balancer-overview.md).

I bilden nedan visas följande konfiguration av distributions exemplet som beskrivs i den här artikeln:

- NVA distribueras i backend-poolen för en intern belastningsutjämnare bakom konfigurationerna för hög tillgänglighets port. 
- Den användardefinierade vägen (UDR) som används i DMZ-undernätet dirigerar all trafik till NVA genom att göra nästa hopp som den interna belastnings utjämningens virtuella IP-adress. 
- Den interna belastningsutjämnaren distribuerar trafiken till en av de aktiva NVA enligt algoritmen för belastnings utjämning.
- NVA bearbetar trafiken och vidarebefordrar den till det ursprungliga målet i backend-undernätet.
- Retur Sök vägen kan ta samma väg om en motsvarande UDR har kon figurer ATS i backend-undernätet. 

![Exempel distribution av portar med hög tillgänglighet](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurera portar med hög tillgänglighet

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera en intern belastningsutjämnare med NVA i backend-poolen om du vill konfigurera portar för hög tillgänglighet. Konfigurera en motsvarande konfiguration av hälso avsöknings konfiguration för belastningsutjämnare för att identifiera NVA-hälsa och belastnings Utjämnings regeln med portar med hög tillgänglighet. Den allmänna belastnings Utjämnings-relaterade konfigurationen beskrivs i [komma igång](load-balancer-get-started-ilb-arm-portal.md). I den här artikeln beskrivs konfigurationerna för hög tillgänglighets port.

Konfigurationen innebär i princip att ställa in frontend-porten och Server delens port värde till **0**. Ange värdet för protokollet till **alla**. Den här artikeln beskriver hur du konfigurerar portar med hög tillgänglighet med hjälp av Azure Portal, PowerShell och Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurera en belastnings Utjämnings regel med hög tillgänglighets port med Azure Portal

Om du vill konfigurera portar med hög tillgänglighet med hjälp av Azure Portal väljer du kryss rutan **ha portar** . När det här alternativet väljs fylls den relaterade porten och protokoll konfigurationen i automatiskt. 

![Konfiguration av portar med hög tillgänglighet via Azure Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurera en belastnings Utjämnings regel med hög tillgänglighets port via Resource Manager-mallen

Du kan konfigurera portar med hög tillgänglighet med hjälp av 2017-08-01 API-versionen för Microsoft. Network/belastningsutjämnare i Load Balancer resursen. Följande JSON-kodfragment illustrerar ändringarna i belastnings Utjämnings konfigurationen för portar med hög tillgänglighet via REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurera en belastnings Utjämnings regel med hög tillgänglighet för portar med PowerShell

Använd följande kommando för att skapa en belastnings Utjämnings regel för hög tillgänglighets port när du skapar den interna belastningsutjämnaren med PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurera en belastnings Utjämnings regel med hög tillgänglighet för portar med Azure CLI

I steg 4 i [skapa en intern belastnings Utjämnings uppsättning](load-balancer-get-started-ilb-arm-cli.md)använder du följande kommando för att skapa belastnings Utjämnings regeln för hög tillgänglighets port:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).