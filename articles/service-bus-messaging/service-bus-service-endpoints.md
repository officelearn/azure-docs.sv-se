---
title: Tjänstslutpunkter i virtuella nätverk och regler för Azure Service Bus | Microsoft Docs
description: Lägg till en slutpunkt för Microsoft.ServiceBus till ett virtuellt nätverk.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: clemensv
ms.openlocfilehash: 87a89ffd627454752924bfc69e44da87b071b5e5
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651443"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Använda virtuella nätverksslutpunkter med Azure Service Bus

Integreringen av Service Bus med [tjänstslutpunkter i virtuella nätverk (VNet)] [ vnet-sep] möjliggör säker åtkomst till funktioner för meddelanden från arbetsbelastningar som virtuella datorer som är kopplade till virtuella nätverk , sökväg för trafik i nätverket som skyddas i bägge ändar.

När konfigurerad att vara bunden till minst en tjänstslutpunkt för virtuellt nätverk undernät, respektive Service Bus-namnområdet ska inte längre att ta emot trafik från var som helst utan behörighet virtuella nätverk. Ur virtuellt nätverk konfigurerar bindning av en Service Bus-namnområdet till en tjänstslutpunkt ett isolerat nätverk tunnel från det virtuella undernätet till meddelandetjänsten.

Resultatet är en privata och isolerade relation mellan de arbetsbelastningar som är bundna till undernätet och respektive Service Bus-namnområdet, trots synliga nätverksadressen för den asynkrona service slutpunkt i en offentlig IP-adressintervallet.

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Service Bus.
>
> Betrodda Microsoft-tjänster inte stöds när virtuella nätverk som implementeras och görs tillgänglig snart.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan är **inte** uttömmande)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT Device Explorer
> - Azure-datautforskaren
>
> Den nedan Microsoft services måste vara i ett virtuellt nätverk
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Virtuella nätverk stöds bara i [premiumnivån](service-bus-premium-messaging.md) Service Bus-namnområden.

## <a name="enable-service-endpoints-with-service-bus"></a>Aktivera Tjänsteslutpunkter med Service Bus

Ett viktigt övervägande när du använder VNet-tjänstslutpunkter med Service Bus är att du inte aktiverar de här slutpunkterna i program som blandar Standard och Premium-nivån Service Bus-namnområden. Slutpunkten är begränsad till Premium-nivån namnområden eftersom Standard-nivån inte har stöd för virtuella nätverk. Det virtuella nätverket blockerar trafik till Standard-namnområdet.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade scenarier som använder VNet-integrering 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernätverk ger segmentering mellan tjänsterna som compartmentalized behöver oftast fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS via TCP/IP, bär risken för problem från nätverket på upp. Meddelandetjänster ger helt isolerade kommunikationsvägar, där även meddelanden skrivs till disk när de överför mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som är både bundna till samma Service Bus-instans kan kommunicera på ett effektivt och tillförlitligt sätt via meddelanden, medan respektive nätverk isolering gräns integritet bevaras.
 
Det innebär att din säkerhet som är känsliga molnlösningar inte bara tillgång till Azure branschledande pålitliga och skalbara asynkrona meddelandefunktioner, men de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säker lösning compartments som är säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS för säker socket-protokoll.

## <a name="binding-service-bus-to-virtual-networks"></a>Bindningen Service Bus till virtuella nätverk

*Virtuella Nätverksregler* är säkerhetsfunktion för brandväggen som styr om Azure Service Bus-server tar emot anslutningar från ett visst virtuellt nätverksundernät.

Bindning av en Service Bus-namnområde till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en **tjänstslutpunkt för virtuellt nätverk** på ett undernät för virtuellt nätverk och aktivera den för ”Microsoft.ServiceBus” som beskrivs i den [endpoint tjänstöversikt] [ vnet-sep]. När du har lagt till tjänsteslutpunkt kan du binda Service Bus-namnrymden till den med en *virtuell nätverksregel*.

Regel för virtuella nätverk är ett nätverk med Service Bus-namnområde med ett virtuellt nätverksundernät. När regeln finns har alla arbetsbelastningar som är bunden till undernätet beviljats åtkomst till Service Bus-namnområdet. Service Bus själva aldrig upprättar utgående anslutningar, behöver inte komma åt och därför beviljas aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk i en befintlig Service Bus-namnrymd.

Mallparametrar:

* **namespaceName**: Service Bus-namnområde.
* **virtualNetworkingSubnetId**: Fullständiga Resource Manager-sökvägen för virtuella nätverkets undernät; till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernät i ett virtuellt nätverk.

> [!NOTE]
> Det finns inga neka regler som är möjligt, Azure Resource Manager-mallen har den standardåtgärd som har angetts till **”Tillåt”** som inte begränsar anslutningar.
> När du skapar regler för virtuellt nätverk eller brandväggar, vi måste ändra den ***”defaultAction”***
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

Om du vill distribuera mallen genom att följa anvisningarna för [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänstslutpunkter i virtuella Azure-nätverket][vnet-sep]
- [Azure Service Bus IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md