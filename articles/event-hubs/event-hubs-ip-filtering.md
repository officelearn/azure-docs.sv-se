---
title: Azure Event Hubs IP filtrerar | Microsoft Docs
description: Använd IP-filtrering för blockerar anslutningar från specifika IP-adresser till Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: spelluru
ms.openlocfilehash: d0114821b5239146f64dde0b01652dc320994585
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408157"
---
# <a name="use-ip-filters"></a>IP-filter

För scenarier där Azure Event Hubs måste vara endast tillgängliga från vissa välkända platser, den *IP-adressfilter* funktionen kan du konfigurera regler för avvisar eller tar emot trafik som kommer från specifika IPv4-adresser. Dessa adresser kan exempelvis vara de för en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Två viktiga användningsfall där det är bra att blockera Event Hubs för vissa IP-adresser är följande:

- Händelsehubbar ska ta emot trafik från ett angivet intervall med IP-adresser och avvisa allt annat. Exempel: du använder Event Hubs med [Azure Express Route] [ express-route] skapa privata anslutningar till din lokala infrastruktur. 
- Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av Event Hubs-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på namnområdesnivå Event Hubs. Därför gäller reglerna för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel i Event Hubs-namnområdet avvisas som ej behörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portalen för Event Hubs är tomt. Den här standardinställningen innebär att din händelsehubb tar emot anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

Om du vill acceptera adresserna i intervallet 70.37.104.0/24 och avvisa allt annat, bör den första regeln i rutnätet godkänna adressintervallet 70.37.104.0/24. Nästa regel ska avvisa alla adresser med hjälp av adressintervallet 0.0.0.0/0.

> [!NOTE]
> Avvisa IP-adresser kan det förhindra att interagera med Händelsehubbar andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Device Explorer i portalen).

### <a name="creating-an-ip-filter-rule-with-azure-resource-manager-templates"></a>Skapa en IP-filterregeln med Azure Resource Manager-mallar

> [!IMPORTANT]
> Virtuella nätverk stöds i **standard** och **dedikerade** nivåerna för Event Hubs. Det stöds inte på basic-nivå. 

Följande Resource Manager-mallen gör det möjligt att lägga till en IP-filterregeln i en befintlig Event Hubs-namnområdet.

Mallparametrar:

- **ipFilterRuleName** måste vara en unik, skiftlägesokänslig, alfanumerisk sträng högst 128 tecken.
- **ipFilterAction** är antingen **avvisa** eller **acceptera** som åtgärden som ska användas för IP-filterregeln.
- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. Till exempel i CIDR representerar notation 70.37.104.0/24 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255 med 24 som anger antalet bitar betydande prefixet för intervallet.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
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