---
title: Filtrering baserad på Azure Firewall Threat Intelligence
description: Hotinformationsbaserad filtrering kan aktiveras för brandväggen för att avisera och avvisa trafik från/till kända skadliga IP-adresser och domäner.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168669"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall Threat Intelligence-baserad filtrering

Hotinformationsbaserad filtrering kan aktiveras för brandväggen för att avisera och avvisa trafik från/till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-feeden. [Intelligent Security graphs](https://www.microsoft.com/en-us/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.

![Hot information för brand vägg](media/threat-intel/firewall-threat.png)

Om du har aktiverat Hot information-baserad filtrering bearbetas de associerade reglerna före någon av NAT-reglerna, nätverks reglerna eller program reglerna.

Du kan välja att bara logga en avisering när en regel utlöses, eller så kan du välja avisering och neka läge.

Som standard är Hot information-baserad filtrering aktive rad i varnings läge. Du kan inte stänga av den här funktionen eller ändra läget tills Portal gränssnittet blir tillgängligt i din region.

![Gränssnitt för Threat Intelligence-baserad filtrerings Portal](media/threat-intel/threat-intel-ui.png)

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

- Se [exempel för Azure Firewall-Log Analytics](log-analytics-samples.md)
- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md)
- Granska [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)