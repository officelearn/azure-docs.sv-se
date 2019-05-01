---
title: Azure Service Bus-brandväggsregler | Microsoft Docs
description: Hur du använder brandväggsregler för att tillåta anslutningar från specifika IP-adresser till Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 540435e3e018ae77477030ae8b9f727d71782121
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704577"
---
# <a name="use-firewall-rules"></a>Använd brandväggsregler

För scenarier där Azure Service Bus är endast tillgänglig från vissa välkända webbplatser, kan brandväggsregler du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Dessa adresser kan exempelvis vara de för en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Om du vill att installationsprogrammet för Service Bus så att den ska ta emot trafik från ett angivet intervall med IP-adresser och avvisa allt annat, och sedan kan du utnyttja en *brandväggen* att blockera Service Bus-slutpunkter från andra IP-adresser. Exempel: du använder Service Bus med [Azure Express Route] [ express-route] skapa privata anslutningar till din lokala infrastruktur. 

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på namnområdesnivå Service Bus. Därför gäller reglerna för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som inte matchar en tillåtna IP-regel i en Service Bus namnrymd avvisas som ej behörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portal för Service Bus är tomt. Den här standardinställningen innebär att ditt namnområde godtar anslutningar på IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

>[!WARNING]
> Implementera brandväggsregler kan det förhindra att interagera med Service Bus andra Azure-tjänster.
>
> Betrodda Microsoft-tjänster inte stöds när IP-filtrering (brandväggsregler) implementeras och görs tillgänglig snart.
>
> Vanliga Azure-scenarier som inte fungerar med IP-filtrering (Observera att listan är **inte** uttömmande)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub Routes
> - Azure IoT Device Explorer
> - Azure-datautforskaren
>
> Den nedan Microsoft services måste vara i ett virtuellt nätverk
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk och brandvägg med Azure Resource Manager-mallar

> [!IMPORTANT]
> Brandväggar och virtuella nätverk stöds bara i den **premium** nivån av Service Bus.

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk i en befintlig Service Bus-namnrymd.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Begränsa åtkomst till Service Bus till Azure-nätverk finns i följande länk:

- [Tjänstslutpunkter i virtuella nätverk för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
