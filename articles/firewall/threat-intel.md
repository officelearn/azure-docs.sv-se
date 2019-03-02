---
title: Filtrering baserad på Azure hotinformation för brandvägg
description: Lär dig mer om Azure-brandvägg threat intelligence filtrering
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/2/2019
ms.author: victorh
ms.openlocfilehash: 38b60536a05fc726ddcd06b631078c0a5f3d89ce
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249152"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure brandväggen threat intelligence-baserad filtrering – förhandsversion

Threat intelligence-baserad filtrering kan aktiveras för brandväggen för att Avisera och nekar trafik från/till känd skadlig IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Hotinformationen feed. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) Driver Microsofts hotintelligens och används av flera tjänster, inklusive Azure Security Center.

![Brandväggen hotinformation](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence filtrering baserad är för närvarande i offentlig förhandsversion och ingår ett serviceavtal för förhandsversionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Threat intelligence filtrering är tillgängligt i alla offentliga regioner. Associerade portalens konfigurationsanvändargränssnittet läggs till regioner stegvis och blir tillgängliga i alla regioner inom en snar framtid.

Om threat intelligence-baserad filtrering är aktiverat, bearbetas de associerade reglerna innan någon av NAT-regler, regler eller regler för program. I förhandsversionen inkluderas endast högsta förtroende poster.

Du kan välja att bara logga in en avisering när en regel utlöses, eller du kan välja aviseringen och neka läge.

Threat intelligence-baserad filtrering är aktiverat som standard i läget för aviseringen. Du kan inte stänga av funktionen eller ändra läget tills portalgränssnittet blir tillgänglig i din region.

![Hotinformation baserad filtrering portalgränssnitt](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

Följande log utdrag visar en utlösta regel:

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

- **Utgående testning** -aviseringar för utgående trafik ska vara sällan förekommande, eftersom det innebär att din miljö har komprometterats. För att arbetar testa utgående aviseringar, ett test FQDN har skapats som utlöser en avisering. Använd **testmaliciousdomain.eastus.cloudapp.azure.com** för dina utgående tester.

- **Inkommande testning** – du kan förvänta dig att se aviseringar på inkommande trafik om DNAT regler har konfigurerats i brandväggen. Detta gäller även om endast särskilda källor tillåts på DNAT regeln och trafik nekas annars. Azure-brandväggen varnar inte om alla kända port skannrar. endast på skannrar som har visat sig också bedriva skadlig aktivitet.

## <a name="next-steps"></a>Nästa steg

- Se [Azure brandväggen Log Analytics-exempel](log-analytics-samples.md)
- Lär dig hur du [distribuera och konfigurera en brandvägg för Azure](tutorial-firewall-deploy-portal.md)
- Granska den [Microsoft Security intelligence report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)