---
title: Översikt över Azure-brandväggsloggar och mått
description: Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315039"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall-loggar och mått

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Du kan komma åt vissa av de här loggarna via portalen. Du kan skicka loggar till [Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md), Storage och Event Hubs samt analysera dem i Azure Monitor-loggar eller med andra verktyg såsom Excel och Power BI.

Mått är lätta och kan stödja scenarier i nära realtid vilket gör dem användbara för aviseringar och snabb problemidentifiering.

## <a name="diagnostic-logs"></a>Diagnostikloggar

 Följande diagnostiska loggar är tillgängliga för Azure Firewall:

* **Programregelloggen**

   Programregelloggen sparas i ett lagringskonto, strömmas till händelsehubbar och/eller skickas till Azure Monitor-loggar endast om du har aktiverat den för varje Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade programreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Nätverksregellogg**

   Nätverksregelloggen sparas i ett lagringskonto, strömmas till händelsehubbar och/eller skickas till Azure Monitor-loggar endast om du har aktiverat det för varje Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade nätverksreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Du har tre alternativ för att lagra dina loggar:

* **Storage-konto**: Storage-konton passar bäst när loggarna ska lagras en längre tid och granskas vid behov.
* **Händelsehubbar**: Händelsehubbar är ett bra alternativ vid integrering med andra verktyg för säkerhetsinformation och händelsehantering (SEIM), när du vill få aviseringar om dina resurser.
* **Azure Monitor loggar:** Azure Monitor loggar används bäst för allmän realtidsövervakning av ditt program eller titta på trender.

## <a name="activity-logs"></a>Aktivitetsloggar

   Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.

   Du kan använda [Azure-aktivitetsloggar](../azure-resource-manager/management/view-activity-logs.md) (tidigare kallade driftloggar och granskningsloggar) för att visa alla åtgärder som skickas till din Azure-prenumeration.

## <a name="metrics"></a>Mått

Mått i Azure Monitor är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. Mått samlas in varje minut och är användbara för aviseringar eftersom de kan samplas ofta. En avisering kan avfyras snabbt med relativt enkel logik.

Följande mått är tillgängliga för Azure-brandväggen:

- **Antal programregler** – Antalet gånger en programregel har nåtts.

    Enhet: antal

- **Antal nätverksregler** – Antalet gånger en nätverksregel har nåtts.

    Enhet: antal

- **Data som bearbetas** - Mängden data som passerar brandväggen.

    Enhet: byte

- **Brandväggshälsotillstånd** - Anger hälsotillståndet för brandväggen baserat på SNAT-porttillgänglighet.

    Enhet: procent

   Det här måttet har två dimensioner:
  - Status: Möjliga värden är *felfria*, *försämrade*, *ohälsosamma*.
  - Orsak: Anger orsaken till brandväggens motsvarande status. 

     Om SNAT-portar används > 95 %, anses de vara uttömda och hälsan är 50 % med status=**Försämrad** och orsak=**SNAT-port**. Brandväggen fortsätter att bearbeta trafik och befintliga anslutningar påverkas inte. Nya anslutningar kanske inte upprättas periodvis.

     Om SNAT-portar används < 95 %, betraktas brandväggen som felfri och hälsotillstånd visas som 100 %.

     Om ingen SNAT-portanvändning rapporteras visas hälsotillståndet som 0 %. 

- **SNAT-portanvändning** - Procentandelen SNAT-portar som har använts av brandväggen.

    Enhet: procent

   När du lägger till fler offentliga IP-adresser i brandväggen finns fler SNAT-portar tillgängliga, vilket minskar användningen av SNAT-portar. Dessutom, när brandväggen skalas ut av olika skäl (till exempel CPU eller dataflöde) ytterligare SNAT-portar blir också tillgängliga. Så effektivt, en viss procentandel av SNAT-portar utnyttjande kan gå ner utan att du lägger till några offentliga IP-adresser, bara för att tjänsten skalas ut. Du kan styra direkt antalet offentliga IP-adresser som är tillgängliga för att öka de portar som är tillgängliga i brandväggen. Men du kan inte direkt styra skalning av brandväggar. För närvarande läggs SNAT-portar bara till för de första fem offentliga IP-adresserna.   


## <a name="next-steps"></a>Nästa steg

- Mer information om hur du övervakar Azure-brandväggsloggar och mått finns i [Självstudiekurs: Övervaka Azure-brandväggsloggar](tutorial-diagnostics.md).

- Mer information om mått i Azure Monitor finns [i Mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
