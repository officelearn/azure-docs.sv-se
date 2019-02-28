---
title: Översikt över Azure brandväggsloggar
description: Den här artikeln är en översikt över diagnostikloggar för Azure-brandvägg.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957699"
---
# <a name="azure-firewall-logs"></a>Loggar från brandväggen för Azure

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Du kan komma åt vissa av de här loggarna via portalen. Loggar kan skickas till [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), lagring och Event Hubs och analyseras i Azure Monitor-loggar och av olika verktyg som Excel och Power BI.

## <a name="diagnostic-logs"></a>Diagnostikloggar

 Följande diagnostiska loggar är tillgängliga för Azure Firewall:

* **Programregelloggen**

   Regeln programloggen sparas till ett lagringskonto, strömma till Event hubs och/eller skickas till Azure Monitor-loggar endast om du har aktiverat det för varje Azure-brandväggen. Varje ny anslutning som matchar en av de konfigurerade programreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

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

   Regel-loggen nätverk sparas i ett lagringskonto, strömma till Event hubs och/eller skickas till Azure Monitor-loggar endast om du har aktiverat det för varje Azure-brandväggen. Varje ny anslutning som matchar en av de konfigurerade nätverksreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

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

* **Storage-konto**: Storage-konton är bäst för loggar när loggar lagras under en längre period och granskat när det behövs.
* **Händelsehubbar**: Händelsehubbar är ett bra alternativ för att integrera med andra säkerhet och händelsehantering (SEIM) hanteringsverktyg för att få meddelanden om dina resurser.
* **Azure Monitor-loggar**: Azure Monitor-loggar är bäst för allmän realtidsövervakning av ditt program eller leta efter trender.

## <a name="activity-logs"></a>Aktivitetsloggar

   Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.

   Du kan använda [Azure-aktivitetsloggar](../azure-resource-manager/resource-group-audit.md) (kallades tidigare för driftloggar och granskningsloggar) till att visa alla åtgärder som skickas till din Azure-prenumeration.


## <a name="next-steps"></a>Nästa steg

Läs hur du övervakar Azure brandväggen loggar och mått i [självstudien: Övervaka Azure brandväggsloggar](tutorial-diagnostics.md).