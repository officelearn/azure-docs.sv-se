---
title: Azure Service Bus IP-anslutningsfilter | Microsoft Docs
description: Hur du använder IP-filtrering ska blockera anslutningar från särskilda IP-adresser till Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: e009bb9120fafc6edf60b68fab3336b9d1add507
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036354"
---
# <a name="use-ip-filters"></a>IP-filter

För scenarier där Azure Service Bus är enbart tillgänglig från vissa välkända webbplatser i *IP-filter* funktionen kan du konfigurera regler för avvisa eller ta emot trafik från specifika IPv4-adresser. Till exempel kanske adresserna de en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall där det är praktiskt att blockera Service Bus-slutpunkter för vissa IP-adresser:

- Service Bus bör endast ta emot trafik från ett angivet IP-adresser och avvisa allt annat. Till exempel du använder Service Bus med [Azure Express Route] [ express-route] att skapa privata anslutningar till din lokala infrastruktur.
- Behöver du avvisa trafik från IP-adresser som har identifierats som misstänkt av Service Bus-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filter regler

IP-filterregler tillämpas på nivån för Service Bus-namnrymd. Därför gäller regler för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel på Service Bus-namnrymd avvisas som obehörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-Filter** rutnät i portal för Service Bus är tom. Den här standardinställningen innebär att namnområdet accepterar anslutningar IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter regeln utvärdering

IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden Godkänn eller avvisa.

Om du vill acceptera adresser i intervallet 70.37.104.0/24 och avvisa allt annat ska den första regeln i rutnätet acceptera adressintervallet 70.37.104.0/24. Nästa regel bör Ignorera alla adresser med hjälp av intervallet 0.0.0.0/0.

> [!NOTE]
> Neka IP-adresser kan förhindra andra Azure-tjänster (till exempel Azure Stream Analytics, Azure virtuella datorer eller enheter Explorer i portalen) interagerar med Service Bus.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuellt nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mall gör det möjligt att lägga till en regel för virtuellt nätverk i en befintlig Service Bus-namnrymd.

Mallparametrar:

- **ipFilterRuleName** måste vara en unik, skiftlägeskänsliga, alfanumeriska sträng upp till 128 tecken.
- **ipFilterAction** är antingen **avvisa** eller **acceptera** som åtgärden som ska gälla för regeln för IP-filter.
- **ipMask** är en IPv4-adress eller ett block av IP-adresser i CIDR-notation. Till exempel i CIDR representerar notation 70.37.104.0/24 256 IPv4-adresser från 70.37.104.0 70.37.104.255 med 24 som anger antalet bitar som betydande prefixet för intervallet.

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

Följ instruktionerna för att distribuera mallen [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Begränsningsaspekten åtkomst till Service Bus till virtuella Azure-nätverk finns på följande länk:

- [Virtuellt nätverksslutpunkter för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services