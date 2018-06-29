---
title: Azure Event Hubs IP-anslutningsfilter | Microsoft Docs
description: Använd IP-filtrering för som blockerar anslutningar från särskilda IP-adresser till Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 425a5b641fbfd2e52e1294c6317b51ff2a584aa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036395"
---
# <a name="use-ip-filters"></a>IP-filter

För scenarier där Azure Event Hubs bara är tillgänglig från vissa välkända webbplatser i *IP-filter* funktionen kan du konfigurera regler för avvisa eller ta emot trafik från specifika IPv4-adresser. Till exempel kanske adresserna de en företagets NAT-gateway.

## <a name="when-to-use"></a>När du ska använda detta

Två viktiga användningsfall där det är praktiskt att blockera Händelsehubbar slutpunkter för vissa IP-adresser är följande:

- Händelsehubbarna bör endast ta emot trafik från ett angivet IP-adresser och avvisa allt annat. Till exempel du använder Händelsehubbar med [Azure Express Route] [ express-route] att skapa privata anslutningar till din lokala infrastruktur. 
- Behöver du avvisa trafik från IP-adresser som har identifierats som misstänkt av Händelsehubbar-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filter regler

IP-filterregler tillämpas på namnområdesnivån Händelsehubbar. Därför gäller regler för alla anslutningar från klienter som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel för Händelsehubbar namnområde avvisas som obehörig. Svaret nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-Filter** rutnätet för Händelsehubbar i portalen är tomt. Den här standardinställningen innebär att din event hub accepterar anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

## <a name="ip-filter-rule-evaluation"></a>IP-filter regeln utvärdering

IP-filterregler tillämpas i ordning, och den första regeln som matchar IP-adressen bestämmer åtgärden Godkänn eller avvisa.

Om du vill acceptera adresser i intervallet 70.37.104.0/24 och avvisa allt annat ska den första regeln i rutnätet acceptera adressintervallet 70.37.104.0/24. Nästa regel bör Ignorera alla adresser med hjälp av intervallet 0.0.0.0/0.

> [!NOTE]
> Neka IP-adresser kan förhindra andra Azure-tjänster (till exempel Azure Stream Analytics, Azure virtuella datorer eller enheter Explorer i portalen) interagera med Händelsehubbar.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuellt nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mall kan lägga till en regel för virtuellt nätverk till ett befintligt namnområde i Händelsehubbar.

Mallparametrar:

- **ipFilterRuleName** måste vara en sträng som unikt, skiftlägeskänsliga, alfanumeriska, upp till 128 tecken.
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

Följ instruktionerna för att distribuera mallen [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Begränsningsaspekten åtkomst till Händelsehubbar till virtuella Azure-nätverk finns på följande länk:

- [Virtual Network Service-slutpunkter för Händelsehubbar][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md