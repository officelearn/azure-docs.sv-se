---
title: Azure Firewall hot intelligens baserad filtrering
description: Hot intelligens-baserad filtrering kan aktiveras för brandväggen att varna och neka trafik från / till kända skadliga IP-adresser och domäner.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168669"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Intelligensbaserad filtrering av Azure Firewall-hot

Hot intelligens-baserad filtrering kan aktiveras för brandväggen att varna och neka trafik från / till kända skadliga IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) driver Microsofts hotinformation och används av flera tjänster, inklusive Azure Security Center.

![Intelligens för brandhot](media/threat-intel/firewall-threat.png)

Om du har aktiverat filtrering baserad på hotinformation bearbetas de associerade reglerna före någon av NAT-reglerna, nätverksreglerna eller programreglerna.

Du kan välja att bara logga en avisering när en regel utlöses, eller så kan du välja varnings- och neka-läge.

Som standard aktiveras filtrering av hotinformation i varningsläge. Du kan inte stänga av den här funktionen eller ändra läget förrän portalgränssnittet blir tillgängligt i din region.

![Hot intelligens baserat filtrering portal gränssnitt](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Loggar

Följande loggutdrag visar en utlöst regel:

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

- **Utgående testning** - Utgående trafikvarningar bör vara en sällsynt händelse, eftersom det innebär att din miljö har komprometterats. För att testa utgående aviseringar fungerar har ett test FQDN skapats som utlöser en avisering. Använd **testmaliciousdomain.eastus.cloudapp.azure.com** för dina utgående tester.

- **Inkommande testning** - Du kan förvänta dig att se aviseringar om inkommande trafik om DNAT-regler är konfigurerade i brandväggen. Detta gäller även om endast specifika källor tillåts på DNAT-regeln och trafik nekas annars. Azure-brandväggen varnar inte för alla kända portskannrar. endast på skannrar som är kända för att också delta i skadlig aktivitet.

## <a name="next-steps"></a>Nästa steg

- Se [exempel på Azure Firewall Log Analytics](log-analytics-samples.md)
- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md)
- Granska [microsoft security intelligence-rapporten](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)