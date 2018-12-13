---
title: Azure Event Hubs-brandväggsregler | Microsoft Docs
description: Använd brandväggsregler för att tillåta anslutningar från specifika IP-adresser till Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 707290d7bf453ca71dd3c5cf8b39c917b3a1c479
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268282"
---
# <a name="use-firewall-rules"></a>Använd brandväggsregler

För scenarier där Azure Event Hubs måste vara endast tillgängliga från vissa välkända platser, kan brandväggsregler du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Dessa adresser kan exempelvis vara de för en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Om du vill konfigurera Event Hubs-namnområdet, till exempel som det ska ta emot trafik från bara angivna intervall med IP-adresser och avvisa allt annat så kan du utnyttja en *brandväggsregel* att blockera Event Hub-slutpunkter från andra IP-adresser. Exempel: du använder Event Hubs med [Azure Express Route] [ express-route] skapa privata anslutningar till din lokala infrastruktur.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på namnområdesnivå Event Hubs. Därför gäller reglerna för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som inte matchar en tillåtna IP-regel i Händelsehubbar namnområde avvisas som ej behörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portalen för Event Hubs är tomt. Den här standardinställningen innebär att din händelsehubb tar emot anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

>[!WARNING]
> Implementera brandväggar kan det förhindra att interagera med Händelsehubbar andra Azure-tjänster.
>
> Betrodda Microsoft-tjänster inte stöds när IP-filtrering (brandväggar) implementeras och görs tillgänglig snart.
>
> Vanliga Azure-scenarier som inte fungerar med IP-filtrering (Observera att listan är **inte** uttömmande)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT Device Explorer
> - Azure-datautforskaren
>
> Den nedan Microsoft services måste vara i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Skapa en brandväggsregel med Azure Resource Manager-mallar

> [!IMPORTANT]
> Brandväggsregler stöds i **standard** och **dedikerade** nivåerna för Event Hubs. Det stöds inte på basic-nivå.

Följande Resource Manager-mallen gör det möjligt att lägga till en IP-filterregeln i en befintlig Event Hubs-namnområdet.

Mallparametrar:

- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. Till exempel i CIDR representerar notation 70.37.104.0/24 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255 med 24 som anger antalet bitar betydande prefixet för intervallet.

> [!NOTE]
> Det finns inga neka regler som är möjligt, Azure Resource Manager-mallen har den standardåtgärd som har angetts till **”Tillåt”** som inte begränsar anslutningar.
> När du skapar regler för virtuellt nätverk eller brandväggar, vi måste ändra den ***”defaultAction”***
> 
> från
> ```json
> "defaultAction": "Allow"
> ```
> till
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Om du vill distribuera mallen genom att följa anvisningarna för [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Begränsa åtkomst till Event Hubs till Azure-nätverk finns i följande länk:

- [Tjänstslutpunkter i virtuella nätverk för Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md