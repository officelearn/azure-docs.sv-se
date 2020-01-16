---
title: Regler för Azure Event Hubs-brandvägg | Microsoft Docs
description: Använd brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 769a70cee4f5a1d5d5f77cdd4e55108e3ba40fa1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978700"
---
# <a name="azure-event-hubs---use-firewall-rules"></a>Azure Event Hubs – använda brand Väggs regler

För scenarier där Azure Event Hubs endast ska vara tillgängligt från vissa välkända platser, kan du konfigurera regler för att acceptera trafik från specifika IPv4-adresser med hjälp av brand Väggs regler. Dessa adresser kan exempelvis vara de för en företagets NAT-gateway.

## <a name="when-to-use"></a>Används till att

Om du vill konfigurera Event Hubs namn området så att det endast ska ta emot trafik från ett visst intervall med IP-adresser och neka allt annat, kan du utnyttja en *brand Väggs regel* för att blockera Event Hub-slutpunkter från andra IP-adresser. Om du till exempel använder Event Hubs med [Azure Express Route][express-route]kan du skapa en *brand Väggs regel* för att begränsa trafiken från dina lokala infrastruktur-IP-adresser.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på namnområdesnivå Event Hubs. Därför gäller reglerna för alla anslutningar från klienter som använder alla protokoll som stöds.

Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Event Hubs namn området nekas som obehörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portalen för Event Hubs är tomt. Den här standardinställningen innebär att din händelsehubb tar emot anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

>[!WARNING]
> Implementering av brand väggar kan förhindra andra Azure-tjänster från att samverka med Event Hubs.
>
> Betrodda Microsoft-tjänster stöds inte när IP-filtrering (brand väggar) implementeras och kommer snart att göras tillgänglig.
>
> Vanliga Azure-scenarier som inte fungerar med IP-filtrering (Observera att listan **inte** är fullständig) –
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> De Microsoft-tjänster som behövs nedan måste finnas i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Skapa en brand Väggs regel med Azure Resource Manager mallar

> [!IMPORTANT]
> Brand Väggs regler stöds i **standard** -och **dedikerade** nivåer av Event Hubs. De stöds inte på grundläggande nivå.

Följande Resource Manager-mallen gör det möjligt att lägga till en IP-filterregeln i en befintlig Event Hubs-namnområdet.

Mallparametrar:

- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. Till exempel i CIDR representerar notation 70.37.104.0/24 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255 med 24 som anger antalet bitar betydande prefixet för intervallet.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
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

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Begränsa åtkomst till Event Hubs till Azure-nätverk finns i följande länk:

- [Virtual Network tjänst slut punkter för Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
