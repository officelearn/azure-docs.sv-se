---
title: Azure Service Bus IP-anslutningsfilter | Microsoft Docs
description: Så här använder IP-filtrering för blockerar anslutningar från specifika IP-adresser till Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: ccb759a9151d734aa99a6f6b9c68e6072874dd84
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394836"
---
# <a name="use-ip-filters"></a>IP-filter

För scenarier där Azure Service Bus endast är tillgänglig från vissa välkända platser, den *IP-adressfilter* funktionen kan du konfigurera regler för avvisar eller tar emot trafik som kommer från specifika IPv4-adresser. Dessa adresser kan exempelvis vara de för en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall där det är bra att blockera Service Bus-slutpunkter för vissa IP-adresser:

- Service Bus ska ta emot trafik från ett angivet intervall med IP-adresser och avvisa allt annat. Exempel: du använder Service Bus med [Azure Express Route] [ express-route] skapa privata anslutningar till din lokala infrastruktur.
- Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av Service Bus-administratören.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på namnområdesnivå Service Bus. Därför gäller reglerna för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel i Service Bus-namnområdet avvisas som ej behörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portal för Service Bus är tomt. Den här standardinställningen innebär att ditt namnområde godtar anslutningar på IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

Om du vill acceptera adresserna i intervallet 70.37.104.0/24 och avvisa allt annat, bör den första regeln i rutnätet godkänna adressintervallet 70.37.104.0/24. Nästa regel ska avvisa alla adresser med hjälp av adressintervallet 0.0.0.0/0.

> [!NOTE]
> Avvisa IP-adresser kan det förhindra att interagera med Service Bus andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Device Explorer i portalen).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk i en befintlig Service Bus-namnrymd.

Mallparametrar:

- **ipFilterRuleName** måste vara en unik, skiftlägesokänslig, alfanumerisk sträng på högst 128 tecken.
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
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
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

Begränsa åtkomst till Service Bus till Azure-nätverk finns i följande länk:

- [Tjänstslutpunkter i virtuella nätverk för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services