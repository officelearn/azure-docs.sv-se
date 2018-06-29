---
title: Virtuella nätverksslutpunkter och regler för Händelsehubbar i Azure | Microsoft Docs
description: Lägg till en Microsoft.ServiceBus tjänstslutpunkt till ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: a23e5414cd3c60192badfee65b14c49cd5e96f4e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036369"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda slutpunkter för virtuellt nätverk med Azure Event Hubs

Integrering av Händelsehubbar med [virtuella nätverk (VNet) Tjänsteslutpunkter] [ vnet-sep] möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som till exempel virtuella datorer som är kopplade till virtuella nätverk med nätverkssökvägen för trafik som skyddas i båda ändar. 

När du konfigurerat vara bundna till minst ett virtuellt undernät tjänstslutpunkten respektive Händelsehubbar namnområdet inte längre tillåter trafik från var som helst men behörighet virtuella nätverk. Ur virtuellt nätverk konfigurerar bindning ett namnområde för Händelsehubbar till en tjänstslutpunkt ett isolerat nätverk tunnel från det virtuella nätverket till meddelandetjänsten.

Resultatet är en privat och isolerade relation mellan de arbetsbelastningar som är kopplad till undernätet och respektive Händelsehubbar namnområdet, trots synliga nätverksadressen för den asynkrona tjänsten endpoint en offentlig IP-adressintervall.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenarier för avancerad säkerhet aktiveras genom integrering av virtuellt nätverk 

Lösningar som kräver nära och avdelningsvis säkerhet och där virtuella undernät ger segmentering mellan tjänsterna compartmentalized måste oftast fortfarande Kommunikationssökvägar mellan tjänster som finns i dessa utrymmen.

Någon omedelbar IP-väg mellan avdelningar, inklusive de som HTTPS över TCP/IP, bär risken för utnyttjandet av säkerhetsproblem från nätverket på upp. Meddelandetjänster ger helt isolerade Kommunikationssökvägar, där även meddelanden skrivs till disk när de flyttar mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som är både bundna till samma Händelsehubbar-instans kan kommunicera effektivt och tillförlitligt sätt via meddelanden, medan respektive nätverk isolerade gräns integritet bevaras.
 
Det innebär att säkerheten känsliga molnlösningar inte få åtkomst till Azure branschledande tillförlitliga och skalbara asynkron meddelandefunktioner, men de kan nu använda meddelanden för att skapa Kommunikationssökvägar mellan säker lösning compartments som är samtidigt säkrare än vad som kan uppnås med peer-to-peer-kommunikation-läge som helst, inklusive HTTPS och andra TLS-skyddad socket-protokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda Händelsehubbar till virtuella nätverk

*Virtuellt Nätverksregler* är funktionen Brandvägg säkerhet som styr om Händelsehubbar i Azure-servern tar emot anslutningar från ett särskilt virtuellt undernät.

Binda ett namnområde för Händelsehubbar till ett virtuellt nätverk är en tvåstegsprocess. Först måste du skapa en **tjänstslutpunkten för virtuellt nätverk** på ett undernät för virtuellt nätverk och aktivera den för ”Microsoft.ServiceBus” som beskrivs i den [översikt över slutpunkt] [ vnet-sep]. När du har lagt till i tjänsteslutpunkt kan du binda Händelsehubbar namnområdet till den med en *virtuellt nätverk regeln*.

Regeln för virtuellt nätverk är en namngiven associering av Händelsehubbar namnområde med ett undernät för virtuellt nätverk. När regeln finns beviljas alla arbetsbelastningar som är bundna till undernätet åtkomst till namnområdet Händelsehubbar. Händelsehubbar själva aldrig upprättar utgående anslutningar inte behöver komma åt och därför aldrig beviljas åtkomst till ditt undernät genom att aktivera den här regeln.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Skapa en regel för virtuellt nätverk med Azure Resource Manager-mallar

Följande Resource Manager-mall kan lägga till en regel för virtuellt nätverk till ett befintligt namnområde i Händelsehubbar.

Mallparametrar:

* **namespaceName**: Händelsehubbar namnområde.
* **vnetRuleName**: namn på regeln för virtuella nätverk som ska skapas.
* **virtualNetworkingSubnetId**: fullständiga Resource Manager-sökvägen för det virtuella nätverket, till exempel `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standard undernätet för ett virtuellt nätverk.

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

Följ instruktionerna för att distribuera mallen [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella nätverk finns i följande länkar:

- [Slutpunkter för virtuella Azure-nätverket][vnet-sep]
- [Azure Event Hubs IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md