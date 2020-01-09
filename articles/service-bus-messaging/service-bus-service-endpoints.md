---
title: Slut punkter för virtuella nätverks tjänster – Azure Service Bus
description: Den här artikeln innehåller information om hur du lägger till en tjänst slut punkt för Microsoft. Service Bus i ett virtuellt nätverk.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 5446ee12a6933a916444d4f64a0eb983a35a59f8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462054"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Använd Virtual Network tjänst slut punkter med Azure Service Bus

Integreringen av Service Bus med [tjänst slut punkter för virtuella datorer med Virtual Network (VNet)][vnet-sep] ger säker åtkomst till meddelande funktioner från arbets belastningar som virtuella datorer som är kopplade till virtuella nätverk, med den nätverks trafik väg som skyddas i båda ändar.

När den har kon figurer ATS för att bindas till minst en tjänst slut punkt för ett virtuellt nätverk, kommer Service Bus namn området inte längre att acceptera trafik från var som helst, men auktoriserade virtuella nätverk (n). I det virtuella nätverkets perspektiv binder du en Service Bus namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten.

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Service Bus-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall.

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Service Bus.
>
> Betrodda Microsoft-tjänster stöds inte när virtuella nätverk implementeras.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan **inte** är fullständig) –
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> De Microsoft-tjänster som behövs nedan måste finnas i ett virtuellt nätverk
> - Azure Apptjänst
> - Azure Functions

> [!IMPORTANT]
> Virtuella nätverk stöds endast på [Premium-nivå](service-bus-premium-messaging.md) Service Bus namn områden.

## <a name="enable-service-endpoints-with-service-bus"></a>Aktivera tjänst slut punkter med Service Bus

Viktigt att tänka på när du använder VNet-tjänstens slut punkter med Service Bus är att du inte ska aktivera de här slut punkterna i program som blandar standard-och Premium-nivån Service Bus namn områden. Eftersom standard nivån inte stöder virtuella nätverk, är slut punkten begränsad till Premium-nivåns namn område.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade scenarier som använder VNet-integrering 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernätverk ger segmentering mellan tjänsterna som compartmentalized behöver oftast fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS via TCP/IP, bär risken för problem från nätverket på upp. Meddelandetjänster ger helt isolerade kommunikationsvägar, där även meddelanden skrivs till disk när de överför mellan parterna. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Service Bus instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som är möjligt med peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

## <a name="binding-service-bus-to-virtual-networks"></a>Binda Service Bus till virtuella nätverk

*Virtuella nätverks regler* är brand Väggs säkerhetsfunktionen som styr om din Azure Service Bus-Server accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Service Bus namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **Virtual Network tjänst slut punkt** i ett Virtual Network undernät och aktivera den för "Microsoft. Service Bus" som förklaras i [översikten över tjänstens slut punkt][vnet-sep]. När du har lagt till tjänst slut punkten binder du Service Bus namn området till den med en *regel för virtuellt nätverk*.

Den virtuella nätverks regeln är en associering av Service Bus-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Service Bus namn området. Service Bus själva upprättar aldrig utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en virtuell nätverks regel med Azure Resource Manager mallar

Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Service Bus-namnområde.

Mallparametrar:

* **namespaceName**: Service Bus namnrymd.
* **virtualNetworkingSubnetId**: fullständiga Resource Manager-sökvägen för virtuella nätverkets undernät, till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernät i ett virtuellt nätverk.

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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänst slut punkter för Azure Virtual Network][vnet-sep]
- [Azure Service Bus IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
