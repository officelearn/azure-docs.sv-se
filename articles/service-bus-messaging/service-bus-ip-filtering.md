---
title: Konfigurera IP-brandväggsregler för Azure Service Bus
description: Så här använder du brandväggsregler för att tillåta anslutningar från specifika IP-adresser till Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: a20882de34cb306b767959e21327180ff284e658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475951"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Konfigurera IP-brandväggsregler för Azure Service Bus
Som standard är Service Bus-namnområden tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Den här funktionen är användbar i scenarier där Azure Service Bus endast ska vara tillgänglig från vissa välkända platser. Med brandväggsregler kan du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Om du till exempel använder Service Bus med [Azure Express Route][express-route]kan du skapa en **brandväggsregel** för att tillåta trafik från endast dina lokala IP-adresser eller adresser till en FÖRETAGS NAT-gateway. 

## <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen
IP-brandväggsreglerna tillämpas på servicebussnamnområdesnivå. Därför gäller reglerna för alla anslutningar från klienter som använder protokoll som stöds. Alla anslutningsförsök från en IP-adress som inte matchar en tillåten IP-regel på tjänstbussens namnområde avvisas som obehöriga. Svaret nämner inte IP-regeln. IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du använder Azure-portalen för att skapa IP-brandväggsregler för ett servicebussnamnområde. 

1. Navigera till **tjänstbussens namnområde** i [Azure-portalen](https://portal.azure.com).
2. Välj **Nätverksalternativ** på den vänstra menyn. Som standard är alternativet **Alla nätverk** markerat. Din Service Bus-namnområde accepterar anslutningar från valfri IP-adress. Den här standardinställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0/0. 

    ![Brandvägg - Alla nätverk alternativet valt](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Välj alternativet **Markerade nätverk** högst upp på sidan. Gör så här i avsnittet **Brandvägg:**
    1. Välj **Lägg till alternativet För klient-IP-adress** för att ge din nuvarande klient-IP åtkomst till namnområdet. 
    2. För **adressintervall**anger du en specifik IPv4-adress eller ett intervall med IPv4-adress i CIDR-notation. 
    3. Ange om du vill tillåta **betrodda Microsoft-tjänster att kringgå den här brandväggen**. 

        ![Brandvägg - Alla nätverk alternativet valt](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Välj **Spara** i verktygsfältet för att spara inställningarna. Vänta några minuter innan bekräftelsen visas på portalmeddelandena.

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Det här avsnittet innehåller ett exempel på En Azure Resource Manager-mall som skapar ett virtuellt nätverk och en brandväggsregel.

> [!IMPORTANT]
> Brandväggar och virtuella nätverk stöds endast i **premiumnivån** för Service Bus.

Med följande Resource Manager-mall kan du lägga till en virtuell nätverksregel i ett befintligt Service Bus-namnområde.

Mallparametrar:

- **ipMask** är en enda IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefixbitar för intervallet.

> [!NOTE]
> Även om det inte finns några möjliga neka regler, har Azure Resource Manager-mallen standardåtgärden inställd på **"Tillåt"** som inte begränsar anslutningar.
> När du gör regler för virtuella nätverk eller brandväggar måste vi ändra ***"defaultAction"***
> 
> Från
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
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Om du vill distribuera mallen följer du instruktionerna för [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Information om hur du begränsar åtkomsten till Service Bus till virtuella Azure-nätverk finns i följande länk:

- [Slutpunkter för virtual network service-tjänsten för servicebuss][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
