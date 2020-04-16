---
title: Slutpunkter för tjänsten Virtuellt nätverk – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller information om hur du lägger till en Microsoft.EventHub-tjänstslutpunkt i ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390288"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda slutpunkter för tjänsten För virtuella nätverk med Azure Event Hubs

Integreringen av [VNet-tjänstslutpunkter (Event][vnet-sep] Hubs with Virtual Network) Service Endpoints möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som virtuella datorer som är bundna till virtuella nätverk, med nätverkstrafiksökvägen som skyddas i båda ändar.

När den har konfigurerats för att bindas till minst en slutpunkt för virtuella nätverksundernätstjänsten accepterar respektive namnområde för eventhubbar inte längre trafik från var som helst utan auktoriserade undernät i virtuella nätverk. Från det virtuella nätverksperspektivet konfigurerar bindning av ett namnområde för eventhubbar till en tjänstslutpunkt en isolerad nätverkstunnel från det virtuella nätverksundernätet till meddelandetjänsten. 

Resultatet är en privat och isolerad relation mellan arbetsbelastningarna som är bundna till undernätet och respektive händelsehubbarnamnområde, trots att den observerbara nätverksadressen för slutpunkten för meddelandetjänsten är i ett offentligt IP-intervall. Det finns ett undantag från det här beteendet. Om du aktiverar en tjänstslutpunkt som `denyall` standard aktiveras regeln i [IP-brandväggen](event-hubs-ip-filtering.md) som är associerad med det virtuella nätverket. Du kan lägga till specifika IP-adresser i IP-brandväggen för att aktivera åtkomst till den offentliga slutpunkten för händelsehubben. 

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Event Hubs.
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


> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. Det stöds inte i den **grundläggande** nivån.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhetsscenarier som aktiveras av VNet-integrering 

Lösningar som kräver tät och uppdelad säkerhet, och där virtuella nätverksundernät tillhandahåller segmentering mellan de uppdelade tjänsterna, behöver fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Varje omedelbar IP-väg mellan avdelningarna, inklusive de som transporterar HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätskiktet uppåt. Meddelandetjänster tillhandahåller isolerade kommunikationsvägar, där meddelanden till och med skrivs till disk när de övergår mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som båda är bundna till samma Event Hubs-instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive nätverksisoleringsgränsintegritet bevaras.
 
Det innebär att dina säkerhetskänsliga molnlösningar inte bara får tillgång till Azures branschledande tillförlitliga och skalbara asynkrona meddelandefunktioner, utan de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säkra lösningsfack som i sig är säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS-säkrade socketprotokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda händelsehubbar till virtuella nätverk

**Virtuella nätverksregler** är brandväggssäkerhetsfunktionen som styr om namnområdet Azure Event Hubs accepterar anslutningar från ett visst virtuellt nätverksundernät.

Att binda ett namnområde för händelsehubbar till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en slutpunkt för en **virtuell nätverkstjänst** i ett virtuellt nätverks undernät och aktivera den för **Microsoft.EventHub** enligt beskrivningen i översiktsartikeln för [tjänstens slutpunkt.][vnet-sep] När du har lagt till tjänstslutpunkten binder du namnområdet Event Hubs till den med en **virtuell nätverksregel**.

Regeln om virtuellt nätverk är en associering av namnområdet Event Hubs med ett virtuellt nätverksundernät. Medan regeln finns beviljas alla arbetsbelastningar som är bundna till undernätet åtkomst till namnområdet Event Hubs. Event Hubs själv upprättar aldrig utgående anslutningar, behöver inte få åtkomst och beviljas därför aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du använder Azure-portalen för att lägga till en slutpunkt för virtuella nätverkstjänster. Om du vill begränsa åtkomsten måste du integrera slutpunkten för den virtuella nätverkstjänsten för det här namnområdet för eventhubbar.

1. Navigera till **namnområdet Event Hubs** i [Azure-portalen](https://portal.azure.com).
2. Välj **Nätverksalternativ** på den vänstra menyn. Om du väljer alternativet **Alla nätverk** accepterar händelsehubben anslutningar från valfri IP-adress. Den här inställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Brandvägg - Alla nätverk alternativet valt](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Om du vill ge åtkomst till specifika nätverk väljer du alternativet **Markerade nätverk** högst upp på sidan.
2. Välj **+Lägg till befintligt virtuellt nätverk***i avsnittet **Virtuellt nätverk** på sidan. Välj **+ Skapa nytt virtuellt nätverk** om du vill skapa ett nytt virtuellt nätverk. 

    ![lägga till ett befintligt virtuellt nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Välj det virtuella nätverket i listan över virtuella nätverk och välj sedan **undernätet**. Du måste aktivera tjänstslutpunkten innan du lägger till det virtuella nätverket i listan. Om tjänstslutpunkten inte är aktiverad uppmanas du att aktivera den i portalen.
   
   ![välj undernät](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Följande lyckade meddelande bör visas när tjänstslutpunkten för undernätet är aktiverat för **Microsoft.EventHub**. Välj **Lägg till** längst ned på sidan om du vill lägga till nätverket. 

    ![välj undernät och aktivera slutpunkt](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Om du inte kan aktivera tjänstslutpunkten kan du ignorera slutpunkten för den virtuella nätverkstjänsten som saknas med hjälp av resource manager-mallen. Den här funktionen är inte tillgänglig i portalen.
6. Välj **Spara** i verktygsfältet för att spara inställningarna. Vänta några minuter innan bekräftelsen visas på portalmeddelandena.

    ![Spara nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

Med följande Resource Manager-mall kan du lägga till en virtuell nätverksregel i ett befintligt namnområde för eventhubbar.

Mallparametrar:

* **namespaceName**: Namnområde För händelsehubbar.
* **vnetRuleName**: Namn på regeln virtuellt nätverk som ska skapas.
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [IP-filtrering av Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
