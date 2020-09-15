---
title: Konfiguration av Azure Firewall Threat Intelligence
description: Lär dig hur du konfigurerar Hot information-baserad filtrering för din Azure Firewall-princip för att varna och neka trafik från och till kända skadliga IP-adresser och domäner.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530649"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Konfiguration av Azure Firewall Threat Intelligence

Hot information-baserad filtrering kan konfigureras för din Azure Firewall-princip för att varna och neka trafik från och till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. [Intelligent Security graphs](https://www.microsoft.com/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.<br>

Om du har konfigurerat Threat Intelligence-baserad filtrering bearbetas de associerade reglerna före någon av NAT-reglerna, nätverks reglerna eller program reglerna.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Hot informations princip":::

## <a name="threat-intelligence-mode"></a>Hot informations läge

Du kan välja att bara logga en avisering när en regel utlöses, eller så kan du välja avisering och neka läge.

Som standard är Hot information-baserad filtrering aktive rad i varnings läge.

## <a name="allowed-list-addresses"></a>Tillåtna List adresser

Du kan konfigurera en lista över tillåtna IP-adresser så att hot information inte filtrerar de adresser, intervall eller undernät som du anger.



## <a name="logs"></a>Loggar

Följande logg utdrag visar en utlöst regel:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testning

- **Utgående testning** -utgående trafik aviseringar bör vara en sällsynt förekomst, vilket innebär att din miljö har komprometterats. För att testa utgående aviseringar fungerar, har ett test-FQDN skapats som utlöser en avisering. Använd **testmaliciousdomain.eastus.cloudapp.Azure.com** för dina utgående tester.

- **Inkommande testning** – du kan vänta på att se aviseringar på inkommande trafik om DNAt regler har kon figurer ATS i brand väggen. Detta gäller även om endast vissa källor tillåts i DNAT-regeln och trafik nekas annars. Azure-brandväggen varnas inte för alla kända port skannrar. endast på skannrar som är kända för att även delta i skadlig aktivitet.

## <a name="next-steps"></a>Nästa steg

- Granska [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)