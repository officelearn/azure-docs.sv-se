---
title: Tjänstslutpunkter i virtuella nätverk – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du lägger till en slutpunkt för Microsoft.EventHub till ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343451"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda virtuella nätverksslutpunkter med Azure Event Hubs

Integreringen av Event Hubs med [tjänstslutpunkter i virtuella nätverk (VNet)] [ vnet-sep] möjliggör säker åtkomst till funktioner för meddelanden från arbetsbelastningar som till exempel virtuella datorer som är kopplade till virtuella nätverk med nätverkssökvägen för trafik som skyddas i bägge ändar.

När du konfigurerat bundet till minst en tjänstslutpunkt för virtuellt nätverk undernät tar emot trafik från var som helst i respektive Event Hubs-namnområdet inte längre men behörighet undernät i virtuella nätverk. Ur virtuellt nätverk konfigurerar bindning ett namnområde för Event Hubs till en slutpunkt för ett isolerat nätverk tunnel från det virtuella undernätet till meddelandetjänsten. 

Resultatet är en privata och isolerade relation mellan de arbetsbelastningar som är bundna till undernätet och respektive Event Hubs-namnområdet, trots synliga nätverksadressen för den asynkrona service slutpunkt i en offentlig IP-adressintervallet. Det finns ett undantag till detta beteende. Aktiverar en tjänstslutpunkt som standard kan denyall regeln i IP-brandväggen som är associerade med det virtuella nätverket. Du kan lägga till specifika IP-adresser i IP-brandväggen för att ge åtkomst till den offentliga slutpunkten för Event Hub. 


>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Event Hubs.
>
> Betrodda Microsoft-tjänster inte stöds när virtuella nätverk har implementerats.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan är **inte** uttömmande)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub Routes
> - Azure IoT Device Explorer
> - Azure-datautforskaren
>
> Den nedan Microsoft services måste vara i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. De stöds inte på grundläggande nivå.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade scenarier som använder VNet-integrering 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernätverk ger segmentering mellan tjänsterna som compartmentalized behöver fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS via TCP/IP, bär risken för problem från nätverket på upp. Meddelandetjänster ger helt isolerade kommunikationsvägar, där även meddelanden skrivs till disk när de överför mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som är både bundna till samma Event Hubs-instans kan kommunicera på ett effektivt och tillförlitligt sätt via meddelanden, medan respektive nätverk isolering gräns integritet bevaras.
 
Det innebär att din säkerhet som är känsliga molnlösningar inte bara tillgång till Azure branschledande pålitliga och skalbara asynkrona meddelandefunktioner, men de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säker lösning compartments som är säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS för säker socket-protokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda Händelsehubbar till virtuella nätverk

*Virtuella Nätverksregler* är säkerhetsfunktion för brandväggen som styr om Azure Event Hubs-namnområdet accepterar anslutningar från ett visst virtuellt nätverksundernät.

Bindning för ett namnområde för Event Hubs till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en **tjänstslutpunkt för virtuellt nätverk** på ett undernät för virtuellt nätverk och aktivera den för ”Microsoft.EventHub” som beskrivs i den [endpoint tjänstöversikt] [ vnet-sep]. När du har lagt till tjänsteslutpunkt kan du binda Event Hubs-namnområdet till den med en *virtuell nätverksregel*.

Regel för virtuella nätverk är ett nätverk med Event Hubs-namnområde med ett virtuellt nätverksundernät. När regeln finns har alla arbetsbelastningar som är bunden till undernätet beviljats åtkomst till Event Hubs-namnområdet. Händelsehubbar själva aldrig upprättar utgående anslutningar, behöver inte komma åt och därför aldrig beviljas åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk till ett befintligt namnområde för Event Hubs.

Mallparametrar:

* **namespaceName**: Event Hubs-namnområde.
* **vnetRuleName**: Namn för virtuellt nätverk regeln som ska skapas.
* **virtualNetworkingSubnetId**: Fullständiga Resource Manager-sökvägen för virtuella nätverkets undernät; till exempel `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernät i ett virtuellt nätverk.

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
- [Azure Event Hubs IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
