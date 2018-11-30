---
title: Översikt över Azure brandväggsloggar
description: Den här artikeln är en översikt över diagnostikloggar för Azure-brandvägg.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 0698f1dbc491781089ef94eec32f2a427fd3cca4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422396"
---
# <a name="azure-firewall-logs"></a>Loggar från brandväggen för Azure

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Du kan komma åt vissa av de här loggarna via portalen. Du kan skicka loggar till [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Storage och Event Hubs, och analysera dem i Log Analytics eller med andra verktyg som Excel och Power BI.

## <a name="diagnostic-logs"></a>Diagnostikloggar

 Följande diagnostiska loggar är tillgängliga för Azure Firewall:

* **Programregelloggen**

   Programregelloggen sparas till ett lagringskonto, strömmas till Event Hubs och/eller skickas till Log Analytics om du har aktiverat det för respektive Azure Firewall. Varje ny anslutning som matchar en av de konfigurerade programreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

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

   Nätverksregelloggen sparas till ett lagringskonto, strömmas till en händelsehubb och/eller skickas till Log Analytics om du har aktiverat det för respektive Azure Firewall. Varje ny anslutning som matchar en av de konfigurerade nätverksreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

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
* **Log Analytics**: Log Analytics passar bäst till allmän realtidsövervakning av ditt program eller när du vill leta efter trender.

## <a name="activity-logs"></a>Aktivitetsloggar

   Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.

   Du kan använda [Azure-aktivitetsloggar](../azure-resource-manager/resource-group-audit.md) (kallades tidigare för driftloggar och granskningsloggar) till att visa alla åtgärder som skickas till din Azure-prenumeration.


## <a name="next-steps"></a>Nästa steg

Läs hur du övervakar Azure brandväggen loggar och mått i [självstudie: övervaka Azure brandväggsloggar](tutorial-diagnostics.md).