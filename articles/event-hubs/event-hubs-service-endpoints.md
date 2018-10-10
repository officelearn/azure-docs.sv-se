---
title: Tjänstslutpunkter i virtuella nätverk och regler för Azure Event Hubs | Microsoft Docs
description: Lägg till en slutpunkt för Microsoft.EventHub till ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ff0ebbb140627caaaa71c5d09d0a4078eca86055
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888157"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda virtuella nätverksslutpunkter med Azure Event Hubs

Integreringen av Event Hubs med [tjänstslutpunkter i virtuella nätverk (VNet)] [ vnet-sep] möjliggör säker åtkomst till funktioner för meddelanden från arbetsbelastningar som till exempel virtuella datorer som är kopplade till virtuella nätverk med nätverkssökvägen för trafik som skyddas i bägge ändar. 

> [!IMPORTANT]
> Virtuella nätverk stöds i **standard** och **dedikerade** nivåerna för Event Hubs. Det stöds inte på basic-nivå. 

När konfigurerad att vara bunden till minst en tjänstslutpunkt för virtuellt nätverk undernät, de respektive Event Hubs-namnområdet inte längre tar emot trafik från var som helst utan behörighet virtuella nätverk. Ur virtuellt nätverk konfigurerar bindning ett namnområde för Event Hubs till en slutpunkt för ett isolerat nätverk tunnel från det virtuella undernätet till meddelandetjänsten.

Resultatet är en privata och isolerade relation mellan de arbetsbelastningar som är bundna till undernätet och respektive Event Hubs-namnområdet, trots synliga nätverksadressen för den asynkrona service slutpunkt i en offentlig IP-adressintervallet.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade scenarier som använder VNet-integrering 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernätverk ger segmentering mellan tjänsterna som compartmentalized behöver oftast fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS via TCP/IP, bär risken för problem från nätverket på upp. Meddelandetjänster ger helt isolerade kommunikationsvägar, där även meddelanden skrivs till disk när de överför mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som är både bundna till samma Event Hubs-instans kan kommunicera på ett effektivt och tillförlitligt sätt via meddelanden, medan respektive nätverk isolering gräns integritet bevaras.
 
Det innebär att din säkerhet som är känsliga molnlösningar inte bara tillgång till Azure branschledande pålitliga och skalbara asynkrona meddelandefunktioner, men de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säker lösning compartments som är säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS för säker socket-protokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda Händelsehubbar till virtuella nätverk

*Virtuella Nätverksregler* är säkerhetsfunktion för brandväggen som styr om Azure Event Hubs-server tar emot anslutningar från ett visst virtuellt nätverksundernät.

Bindning för ett namnområde för Event Hubs till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en **tjänstslutpunkt för virtuellt nätverk** på ett undernät för virtuellt nätverk och aktivera den för ”Microsoft.EventHub” som beskrivs i den [endpoint tjänstöversikt] [ vnet-sep]. När du har lagt till tjänsteslutpunkt kan du binda Event Hubs-namnområdet till den med en *virtuell nätverksregel*.

Regel för virtuella nätverk är en namngivna nätverk med Event Hubs-namnområde med ett virtuellt nätverksundernät. När regeln finns har alla arbetsbelastningar som är bunden till undernätet beviljats åtkomst till Event Hubs-namnområdet. Händelsehubbar själva aldrig upprättar utgående anslutningar, behöver inte komma åt och därför aldrig beviljas åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuella nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mallen gör det möjligt att lägga till en regel för virtuella nätverk till ett befintligt namnområde för Event Hubs.

Mallparametrar:

* **namespaceName**: Event Hubs-namnområdet.
* **vnetRuleName**: namn för virtuellt nätverk regeln som ska skapas.
* **virtualNetworkingSubnetId**: fullständiga Resource Manager-sökvägen för virtuella nätverkets undernät, till exempel `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standardundernät i ett virtuellt nätverk.

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
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
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
- [Azure Event Hubs IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md