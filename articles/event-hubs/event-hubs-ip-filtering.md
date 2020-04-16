---
title: Brandväggsregler för Azure Event Hubs | Microsoft-dokument
description: Använd brandväggsregler för att tillåta anslutningar från specifika IP-adresser till Azure Event Hubs.
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
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393133"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Konfigurera IP-brandväggsregler för ett namnområde för Azure Event Hubs
Som standard är Event Hubs namnområden tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Den här funktionen är användbar i scenarier där Azure Event Hubs endast bör vara tillgängliga från vissa välkända platser. Med brandväggsregler kan du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Om du till exempel använder Event Hubs med [Azure Express Route][express-route]kan du skapa en **brandväggsregel** för att tillåta trafik från endast dina lokala IP-adresser för infrastruktur. 

>[!WARNING]
> Aktivera IP-filtrering kan förhindra att andra Azure-tjänster interagerar med eventhubbar.
>
> Betrodda Microsoft-tjänster stöds inte när virtuella nätverk implementeras.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (observera att listan **INTE** är uttömmande) -
> - Azure Monitor (diagnostikinställning)
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub Rutter
> - Utforskaren för Azure IoT-enhet
>
> Följande Microsoft-tjänster måste finnas i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen
IP-brandväggsreglerna tillämpas på namnområdesnivån Event Hubs. Därför gäller reglerna för alla anslutningar från klienter som använder protokoll som stöds. Alla anslutningsförsök från en IP-adress som inte matchar en tillåten IP-regel på namnområdet Event Hubs avvisas som obehörigt. Svaret nämner inte IP-regeln. IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du använder Azure-portalen för att skapa IP-brandväggsregler för ett namnområde för eventhubner. 

1. Navigera till **namnområdet Event Hubs** i [Azure-portalen](https://portal.azure.com).
2. Välj **Nätverksalternativ** på den vänstra menyn. Om du väljer alternativet **Alla nätverk** accepterar händelsehubben anslutningar från valfri IP-adress. Den här inställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Brandvägg - Alla nätverk alternativet valt](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Om du vill begränsa åtkomsten till specifika nätverk och IP-adresser väljer du alternativet **Valda nätverk.** Gör så här i avsnittet **Brandvägg:**
    1. Välj **Lägg till alternativet För klient-IP-adress** för att ge din nuvarande klient-IP åtkomst till namnområdet. 
    2. För **adressintervall**anger du en specifik IPv4-adress eller ett intervall med IPv4-adress i CIDR-notation. 
    3. Ange om du vill tillåta **betrodda Microsoft-tjänster att kringgå den här brandväggen**. 

        ![Brandvägg - Alla nätverk alternativet valt](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Välj **Spara** i verktygsfältet för att spara inställningarna. Vänta några minuter innan bekräftelsen visas på portalmeddelandena.


## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

> [!IMPORTANT]
> Brandväggsregler stöds i **standard-** och **dedikerade** nivåer av händelsehubbar. De stöds inte på grundläggande nivå.

Med följande Resource Manager-mall kan du lägga till en IP-filterregel i ett befintligt namnområde för händelsehubbar.

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

Information om hur du begränsar åtkomsten till händelsehubbar till virtuella Azure-nätverk finns i följande länk:

- [Slutpunkter för virtuella nätverkstjänst för händelsehubbar][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
