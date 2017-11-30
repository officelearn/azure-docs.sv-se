---
title: "Konfigurera portar för hög tillgänglighet för Azure belastningsutjämnare | Microsoft Docs"
description: "Lär dig mer om hög tillgänglighet portar för intern trafik på alla portar för belastningsutjämning"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 646ade828e96810bdc3b07d4dc5c0276a1621969
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Hur du konfigurerar portar för hög tillgänglighet för interna belastningsutjämnare

Den här artikeln innehåller ett exempel på distribution av hög tillgänglighet (HA) portar på en intern belastningsutjämnare. Virtuella nätverksinstallationer (NVAs) finns specifika konfigurationer i de motsvarande provider-webbplatserna.

>[!NOTE]
> Hög tillgänglighet portar funktionen är för närvarande under förhandsgranskning. Som förhandsversion kanske funktionen inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bild 1 visar exempel för distribution som beskrivs i den här artikeln följande konfiguration:
- NVAs distribueras i serverdelspoolen av en intern belastningsutjämnare bakom portkonfiguration för hög tillgänglighet. 
- UDR tillämpas på DMZ undernätsvägar all trafik NVAs genom att göra nästa hopp som interna belastningen Belastningsutjämnarens virtuella IP-Adressen. 
- Interna belastningsutjämnare distribuerar trafik till en aktiv NVAs enligt LB-algoritmen.
- NVA bearbetar trafiken och vidarebefordrar det till det ursprungliga målet i backend-undernät.
- Returnerar sökvägen kan också ta samma flöde om motsvarande UDR konfigureras på backend-undernät. 

![hög tillgänglighet portarna exempel på distribution](./media/load-balancer-configure-ha-ports/haports.png)

Bild 1 - nätverket virtuella installationer distribueras bakom en intern belastningsutjämnare med hög tillgänglighet portar 

## <a name="preview-sign-up"></a>Förhandsgranska registrering

Registrera prenumerationen för att få åtkomst med hjälp av Azure CLI 2.0 eller PowerShell för att delta i förhandsversionen av funktionen hög tillgänglighet portar i Load Balancer Standard. Registrera prenumerationen för [Load Balancer Standard preview](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Registrering av belastningen belastningsutjämnaren Standard förhandsversioner kan ta upp till en timme.

## <a name="configuring-ha-ports"></a>Hur du konfigurerar portar för hög tillgänglighet

Konfiguration av portarna för hög tillgänglighet innebär att ställa in en intern belastningsutjämnare, med NVAs i serverdelspoolen, en motsvarande hälsa avsökningen belastningsutjämningskonfigurationen identifiera NVA hälsa och belastningsutjämnare regeln med hög tillgänglighet portar. Allmän konfiguration för belastningsutjämnaren-relaterade beskrivs i [Kom igång](load-balancer-get-started-ilb-arm-portal.md). Den här artikeln visar portkonfiguration för hög tillgänglighet.

Konfigurationen i praktiken innebär att ange värdet för klientdel porten och serverdelsporten port till **0**, och värdet för protokollet till **alla**. Den här artikeln beskriver hur du konfigurerar portar för hög tillgänglighet med hjälp av Azure-portalen, PowerShell och Azure CLI 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurera hög tillgänglighet portar belastningsutjämningsregeln med Azure-portalen

Azure portal innehåller den **HA portar** alternativet via en kryssruta för den här konfigurationen. När du väljer fylls automatiskt relaterade konfigurationen för port och protokoll. 

![hög tillgänglighet portarna konfigurationen via Azure-portalen](./media/load-balancer-configure-ha-ports/haports-portal.png)

Bild 2 – hög tillgänglighet portkonfiguration via portalen

### <a name="configure-ha-ports-lb-rule-via-resource-manager-template"></a>Konfigurera hög tillgänglighet portar LB regel via Resource Manager-mall

Du kan konfigurera hög tillgänglighet portar med hjälp av API-versionen 2017-08-01 för Microsoft.Network/loadBalancers i resursen belastningsutjämnaren. Följande JSON-fragment visar ändringarna i konfigurationen av belastningsutjämnaren för hög tillgänglighet portar via REST API.

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

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Konfigurera hög tillgänglighet portar belastningsutjämningsregeln med PowerShell

Använd följande kommando för att skapa regeln HA portar belastningsutjämnare när du skapar den interna belastningsutjämnaren med PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Konfigurera hög tillgänglighet portar belastningsutjämningsregeln med Azure CLI 2.0

I steg # 4 [att skapa en intern belastning belastningsutjämnaren ställa in](load-balancer-get-started-ilb-arm-cli.md), använder du följande kommando för att skapa hög tillgänglighet portar belastningsutjämnaren regel.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [portar för hög tillgänglighet](load-balancer-ha-ports-overview.md)
