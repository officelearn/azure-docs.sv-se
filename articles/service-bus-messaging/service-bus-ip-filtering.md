---
title: Azure Service Bus brand Väggs regler | Microsoft Docs
description: Hur du använder brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Service Bus.
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
ms.openlocfilehash: 45415af479c9581ee04b97af4fb5297d09c5769d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496327"
---
# <a name="use-firewall-rules"></a>Använd brand Väggs regler

För scenarier där Azure Service Bus endast kan nås från vissa välkända platser, kan du konfigurera regler för att acceptera trafik från specifika IPv4-adresser i brand Väggs regler. Dessa adresser kan till exempel vara de som tillhör en Corporate NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Om du vill konfigurera Service Bus så att den endast ska ta emot trafik från ett visst intervall med IP-adresser och neka allt annat, kan du använda en *brand vägg* för att blockera Service Bus slut punkter från andra IP-adresser. Du använder till exempel Service Bus med [Azure Express Route][express-route] för att skapa privata anslutningar till din lokala infrastruktur. 

## <a name="how-filter-rules-are-applied"></a>Hur filter regler tillämpas

IP-filter regler tillämpas på Service Bus namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds.

Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Service Bus namn området nekas som obehörig. Svaret innehåller ingen IP-regel.

## <a name="default-setting"></a>Standardinställning

Som standard är rutnätet för **IP-filter** i portalen för Service Bus tomt. Den här standardinställningen innebär att namn området accepterar anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Regel utvärdering av IP-filter

IP-filter regler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

>[!WARNING]
> Implementering av brand Väggs regler kan förhindra att andra Azure-tjänster interagerar med Service Bus.
>
> Betrodda Microsoft-tjänster stöds inte när IP-filtrering (brand Väggs regler) implementeras och kommer snart att göras tillgänglig.
>
> Vanliga Azure-scenarier som inte fungerar med IP-filtrering (Observera att listan **inte** är fullständig) –
> - Azure Monitor
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> De Microsoft-tjänster som behövs nedan måste finnas i ett virtuellt nätverk
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Skapa ett virtuellt nätverk och en brand Väggs regel med Azure Resource Manager mallar

> [!IMPORTANT]
> Brand väggar och virtuella nätverk stöds endast på **Premium** -nivån för Service Bus.

Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Service Bus-namnområde.

Mallparametrar:

- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefix för intervallet.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
> 
> som
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

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Information om hur du begränsar åtkomsten till Service Bus till virtuella Azure-nätverk finns i följande länk:

- [Virtual Network tjänst slut punkter för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
