---
title: Konfigurera slutpunkter för virtuella nätverkstjänster för Azure Service Bus
description: Den här artikeln innehåller information om hur du lägger till en Microsoft.ServiceBus-tjänstslutpunkt i ett virtuellt nätverk.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454989"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Konfigurera slutpunkter för virtuella nätverkstjänster för Azure Service Bus

Integreringen av Tjänsten Buss med [VNet-tjänstslutpunkter (Virtual Network)][vnet-sep] möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som virtuella datorer som är bundna till virtuella nätverk, med nätverkstrafiksökvägen som skyddas i båda ändar.

När den har konfigurerats för att vara bunden till minst en slutpunkt för virtuella nätverksundernätstjänsten accepterar respektive tjänstbussnamnområde inte längre trafik från var som helst utan auktoriserade virtuella nätverk. Från det virtuella nätverksperspektivet konfigurerar bindning av ett tjänstbussnamnområde till en tjänstslutpunkt en isolerad nätverkstunnel från det virtuella nätverksundernätet till meddelandetjänsten.

Resultatet är en privat och isolerad relation mellan arbetsbelastningarna som är bundna till undernätet och respektive Service Bus-namnområde, trots att den observerbara nätverksadressen för slutpunkten för meddelandetjänsten är i ett offentligt IP-intervall.

> [!IMPORTANT]
> Virtuella nätverk stöds endast i [premium-nivå](service-bus-premium-messaging.md) servicebussnamnområden.
> 
> När du använder VNet-tjänstslutpunkter med Service Bus bör du inte aktivera dessa slutpunkter i program som blandar standard- och premiumnivåservicebussnamnområden. Eftersom standardnivån inte stöder virtuella nätverk. Slutpunkten är begränsad till endast namnområden på Premium-nivån.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhetsscenarier som aktiveras av VNet-integrering 

Lösningar som kräver tät och uppdelad säkerhet, och där virtuella nätverksundernät tillhandahåller segmentering mellan de uppdelade tjänsterna, behöver i allmänhet fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Varje omedelbar IP-väg mellan avdelningarna, inklusive de som transporterar HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätskiktet uppåt. Meddelandetjänster tillhandahåller helt isolerade kommunikationsvägar, där meddelanden till och med skrivs till disk när de övergår mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som båda är bundna till samma Service Bus-instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive nätverksisoleringsgränsintegritet bevaras.
 
Det innebär att dina säkerhetskänsliga molnlösningar inte bara får tillgång till Azures branschledande tillförlitliga och skalbara asynkrona meddelandefunktioner, utan de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säkra lösningsfack som är i sig säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS-säkrade socketprotokoll.

## <a name="binding-service-bus-to-virtual-networks"></a>Bindningstjänstbuss till virtuella nätverk

*Virtuella nätverksregler* är brandväggssäkerhetsfunktionen som styr om Azure Service Bus-servern accepterar anslutningar från ett visst virtuellt nätverksundernät.

Att binda ett servicebussnamnområde till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en slutpunkt för **tjänsten Virtuellt nätverk** i ett virtuellt nätverk undernät och aktivera den för **Microsoft.ServiceBus** enligt beskrivningen i [tjänstslutpunktsöversikten][vnet-sep]. När du har lagt till tjänstslutpunkten binder du servicebussens namnområde till den med en **virtuell nätverksregel**.

Regeln om virtuellt nätverk är en associering av servicebussnamnområdet med ett virtuellt nätverksundernät. Medan regeln finns beviljas alla arbetsbelastningar som är bundna till undernätet åtkomst till servicebussnamnområdet. Service Bus själv upprättar aldrig utgående anslutningar, behöver inte få åtkomst och beviljas därför aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du använder Azure-portalen för att lägga till en slutpunkt för virtuella nätverkstjänster. Om du vill begränsa åtkomsten måste du integrera slutpunkten för den virtuella nätverkstjänsten för det här namnområdet för eventhubbar.

1. Navigera till **tjänstbussens namnområde** i [Azure-portalen](https://portal.azure.com).
2. Välj **Nätverksalternativ** på den vänstra menyn. Som standard är alternativet **Alla nätverk** markerat. Namnområdet accepterar anslutningar från valfri IP-adress. Den här standardinställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0/0. 

    ![Brandvägg - Alla nätverk alternativet valt](./media/service-endpoints/firewall-all-networks-selected.png)
1. Välj alternativet **Markerade nätverk** högst upp på sidan.
2. I avsnittet **Virtuellt nätverk** på sidan väljer du **+Lägg till befintligt virtuellt nätverk**. 

    ![lägga till ett befintligt virtuellt nätverk](./media/service-endpoints/add-vnet-menu.png)
3. Välj det virtuella nätverket i listan över virtuella nätverk och välj sedan **undernätet**. Du måste aktivera tjänstslutpunkten innan du lägger till det virtuella nätverket i listan. Om tjänstslutpunkten inte är aktiverad uppmanas du att aktivera den i portalen.
   
   ![välj undernät](./media/service-endpoints/select-subnet.png)

4. Följande lyckade meddelande bör visas när tjänstslutpunkten för undernätet är aktiverat för **Microsoft.ServiceBus**. Välj **Lägg till** längst ned på sidan om du vill lägga till nätverket. 

    ![välj undernät och aktivera slutpunkt](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Om du inte kan aktivera tjänstslutpunkten kan du ignorera slutpunkten för den virtuella nätverkstjänsten som saknas med hjälp av resource manager-mallen. Den här funktionen är inte tillgänglig i portalen.
6. Välj **Spara** i verktygsfältet för att spara inställningarna. Vänta några minuter innan bekräftelsen visas i portalmeddelandena. **Knappen Spara** ska inaktiveras. 

    ![Spara nätverk](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Med följande Resource Manager-mall kan du lägga till en virtuell nätverksregel i ett befintligt Service Bus-namnområde.

Mallparametrar:

* **namespaceName**: Service Bus namnområde.
* **virtualNetworkingSubnetId**: Fullständigt kvalificerad resurshanterarens sökväg för det virtuella nätverksundernätet. till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernätet i ett virtuellt nätverk.

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

Mall:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vnet-sep]
- [AZURE Service Bus IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
