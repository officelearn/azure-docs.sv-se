---
title: "Automatiskt skala upp Azure Event Hubs genomflödesenheter | Microsoft Docs"
description: "Aktivera automatisk ökar på ett namnområde för att automatiskt skala upp enheter"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 1cd31e0866ee6088483f88e8f80d01f75764c771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatiskt skala upp Azure Event Hubs genomflödesenheter

Händelsehubbar i Azure är en mycket skalbar dataströmning plattform. Händelsehubbar kunder öka därför ofta sin användning efter onboarding till tjänsten. Detta ökar krävs att du ökar förbestämt genomflödesenheter om du vill skala Event Hubs och hantera större överföringshastighet. Den *automatiskt öka* funktion i Händelsehubbar skalas automatiskt antalet genomflödesenheter användning behov. Öka genomflödesenheter förhindrar begränsning scenarier där:

* Ingång överföringshastighet överskrida uppsättning enheter.
* Överföringshastighet för utgående begäran överskrider uppsättning enheter.

## <a name="how-auto-inflate-works"></a>Så här fungerar automatiskt öka

Event Hubs trafik styrs av genomflödesenheter. En genomflödesenhet kan 1 MB per sekund på inkommande trafik och två gånger att mängden utgång. Standard Händelsehubbar kan konfigureras med 1-20 genomflödesenheter. Öka automatiskt kan du börja litet med minsta obligatoriska genomflödesenheter. Funktionen skalas sedan automatiskt till den maximala gränsen på genomflödesenheter du behöver, beroende på ökade trafiken. Automatiskt öka ger följande fördelar:

- En effektiv skalning mekanism börja litet och skala upp medan du växer.
- Skala automatiskt till den angivna övre gränsen utan begränsning problem.
- Mer kontroll över skalning, som du styr när och hur mycket skala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivera automatisk ökar på ett namnområde

Du kan aktivera eller inaktivera automatisk ökar på ett namnområde med någon av följande metoder:

1. Den [Azure-portalen](https://portal.azure.com).
2. En Azure Resource Manager-mall.

### <a name="enable-auto-inflate-through-the-portal"></a>Aktivera automatisk öka via portalen

När du skapar ett namnområde för Händelsehubbar kan du aktivera funktionen automatiskt öka på ett namnområde:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Med det här alternativet är aktiverat, kan du börja litet på dina enheter och skala upp som kräver din användning. Den övre gränsen för inflationen påverkar inte prissättning, vilken beror på hur många genomflödesenheter som används per timme.

Du kan också aktivera automatisk-ökar med hjälp av den **skala** alternativet på inställningsbladet i portalen:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Aktivera automatisk-ökar med en Azure Resource Manager-mall

Du kan aktivera automatisk ökar under en Azure Resource Manager för malldistribution. Till exempel den `isAutoInflateEnabled` egenskapen **SANT** och ange `maximumThroughputUnits` till 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Fullständig mallen finns i [skapa Händelsehubbar namnområde och aktivera ökar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) mall på GitHub.

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
