---
title: Virtual Network tjänst slut punkter – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du lägger till en Microsoft. EventHub-tjänsteslutpunkt till ett virtuellt nätverk.
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
ms.openlocfilehash: 9b8b3600acc33e177e65002ba69dcf98a20c2253
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555335"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda Virtual Network tjänst slut punkter med Azure Event Hubs

Integreringen av Event Hubs [Virtual Network med tjänst slut punkter][vnet-sep] för virtuella nätverk ger säker åtkomst till meddelande funktioner från arbets belastningar, till exempel virtuella datorer som är kopplade till virtuella nätverk, med sökvägen till nätverks trafiken som skyddas i båda ändar.

När de har kon figurer ATS för att bindas till minst en tjänst slut punkt för virtuellt nätverk, accepterar respektive Event Hubs namn området inte längre trafik från var som helst men auktoriserade undernät i virtuella nätverk. I det virtuella nätverkets perspektiv binder du en Event Hubs namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten. 

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Event Hubs-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall. Det finns ett undantag för det här beteendet. Genom att aktivera en tjänst slut punkt aktiverar som standard denyall-regeln i IP-brandväggen som är associerad med det virtuella nätverket. Du kan lägga till vissa IP-adresser i IP-brandväggen för att ge åtkomst till den offentliga slut punkten för Händelsehubben. 


>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Event Hubs.
>
> Betrodda Microsoft-tjänster stöds inte när virtuella nätverk implementeras.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan **inte** är fullständig) –
> - Integrering med Azure Monitor. Du kan inte strömma diagnostikloggar från **andra** Azure-tjänster till Event Hubs. Du kan dock aktivera Azure Diagnostic-loggar på själva händelsehubben. Det är samma sak när du har aktiverat brand väggen (IP-filtrering).
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> De Microsoft-tjänster som behövs nedan måste finnas i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. De stöds inte på grundläggande nivå.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhets scenarier som aktive ras av VNet-integrering 

Lösningar som kräver tätt och compartmentalized säkerhet, och där undernät för virtuella nätverk tillhandahåller segmentering mellan compartmentalized-tjänsterna, behöver fortfarande kommunikations vägar mellan tjänster som finns i dessa fack.

Alla omedelbara IP-vägar mellan avdelningarna, inklusive de som driver HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätverks skiktet. Meddelande tjänster tillhandahåller fullständigt isolerade kommunikations vägar, där meddelanden är jämnt skrivna till disk när de övergår mellan parter. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Event Hubs instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som är möjligt med peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda Event Hubs till virtuella nätverk

*Virtuella nätverks regler* är säkerhets funktionen för brand vägg som styr om ditt Azure Event Hubs-namnområde accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Event Hubs namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **Virtual Network tjänst slut punkt** i ett Virtual Network undernät och aktivera den för "Microsoft. EventHub" som förklaras i [Översikt över tjänstens slut punkt][vnet-sep]. När du har lagt till tjänst slut punkten binder du Event Hubs namn området till den med en *regel för virtuellt nätverk*.

Den virtuella nätverks regeln är en associering av Event Hubs-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Event Hubs namn området. Event Hubs själva upprättar aldrig utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en virtuell nätverks regel med Azure Resource Manager mallar

Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Event Hubs-namnområde.

Mallparametrar:

* **namespaceName**: Event Hubs namnrymd.
* **vnetRuleName**: namnet på den Virtual Networks regel som ska skapas.
* **virtualNetworkingSubnetId**: fullständigt kvalificerad Resource Manager-sökväg för det virtuella nätverkets undernät; till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standard under nätet för ett virtuellt nätverk.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
> 
> som
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

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänst slut punkter för Azure Virtual Network][vnet-sep]
- [Azure Event Hubs IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
