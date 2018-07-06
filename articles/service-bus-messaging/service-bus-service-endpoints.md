---
title: Tjänstslutpunkter i virtuella nätverk och regler för Azure Service Bus | Microsoft Docs
description: Lägg till en slutpunkt för Microsoft.ServiceBus till ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: clemensv
ms.openlocfilehash: f8874a1d9db754485e9624596465560981bd6425
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858053"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Använda virtuella nätverksslutpunkter med Azure Service Bus

Integreringen av Service Bus med [tjänstslutpunkter i virtuella nätverk (VNet)] [ vnet-sep] möjliggör säker åtkomst till funktioner för meddelanden från arbetsbelastningar som virtuella datorer som är kopplade till virtuella nätverk , sökväg för trafik i nätverket som skyddas i bägge ändar. 

När konfigurerad att vara bunden till minst en tjänstslutpunkt för virtuellt nätverk undernät, respektive Service Bus-namnområdet ska inte längre att ta emot trafik från var som helst utan behörighet virtuella nätverk. Ur virtuellt nätverk konfigurerar bindning av en Service Bus-namnområdet till en tjänstslutpunkt ett isolerat nätverk tunnel från det virtuella undernätet till meddelandetjänsten.

Resultatet är en privata och isolerade relation mellan de arbetsbelastningar som är bundna till undernätet och respektive Service Bus-namnområdet, trots synliga nätverksadressen för den asynkrona service slutpunkt i en offentlig IP-adressintervallet.

## <a name="enable-service-endpoints-with-service-bus"></a>Aktivera Tjänsteslutpunkter med Service Bus

Virtuella nätverk stöds bara i [premiumnivån](service-bus-premium-messaging.md) Service Bus-namnområden. 

Ett viktigt övervägande när du använder VNet-tjänstslutpunkter med Service Bus är att du inte aktiverar de här slutpunkterna i program som blandar Standard och Premium-nivån Service Bus-namnområden. Slutpunkten är begränsad till Premium-nivån namnområden eftersom Standard-nivån inte har stöd för virtuella nätverk. Det virtuella nätverket blockerar trafik till Standard-namnområdet. 

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade scenarier som använder VNet-integrering 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernätverk ger segmentering mellan tjänsterna som compartmentalized behöver oftast fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS via TCP/IP, bär risken för problem från nätverket på upp. Meddelandetjänster ger helt isolerade kommunikationsvägar, där även meddelanden skrivs till disk när de överför mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som är både bundna till samma Service Bus-instans kan kommunicera på ett effektivt och tillförlitligt sätt via meddelanden, medan respektive nätverk isolering gräns integritet bevaras.
 
Det innebär att din säkerhet som är känsliga molnlösningar inte bara tillgång till Azure branschledande pålitliga och skalbara asynkrona meddelandefunktioner, men de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säker lösning compartments som är säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS för säker socket-protokoll.

## <a name="binding-service-bus-to-virtual-networks"></a>Bindningen Service Bus till virtuella nätverk

*Virtuella Nätverksregler* är säkerhetsfunktion för brandväggen som styr om Azure Service Bus-server tar emot anslutningar från ett visst virtuellt nätverksundernät.

Bindning av en Service Bus-namnområde till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en **tjänstslutpunkt för virtuellt nätverk** på ett undernät för virtuellt nätverk och aktivera den för ”Microsoft.ServiceBus” som beskrivs i den [endpoint tjänstöversikt] [ vnet-sep]. När du har lagt till tjänsteslutpunkt kan du binda Service Bus-namnrymden till den med en *virtuell nätverksregel*.

Regel för virtuella nätverk är en namngivna nätverk med Service Bus-namnområde med ett virtuellt nätverksundernät. När regeln finns har alla arbetsbelastningar som är bunden till undernätet beviljats åtkomst till Service Bus-namnområdet. Service Bus själva aldrig upprättar utgående anslutningar, behöver inte komma åt och därför beviljas aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk i en befintlig Service Bus-namnrymd.

Mallparametrar:

* **namespaceName**: Service Bus-namnområde.
* **vnetRuleName**: namn för virtuellt nätverk regeln som ska skapas.
* **virtualNetworkingSubnetId**: fullständiga Resource Manager-sökvägen för virtuella nätverkets undernät, till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernät i ett virtuellt nätverk.

Mall:

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
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
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