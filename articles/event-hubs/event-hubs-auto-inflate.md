---
title: Skala automatiskt upp genomflödesenheter – Azure Event Hubs | Microsoft Docs
description: Aktivera automatisk ökning på ett namnområde för att automatiskt skala upp genomflödesenheter.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d705993c7cd3816e89da21625dc5b003435b9128
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408173"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Skala automatiskt upp Azure Event Hubs-dataflödesenheter
Azure Event Hubs är en mycket skalbar dataströmningsplattform. Event Hubs användning ökar därför ofta när du börjar använda tjänsten. Denna användning kräver ökar den förinställt [genomflödesenheter](event-hubs-features.md#throughput-units) att skalas Event Hubs och hantera större överföringshastighet. Den **automatisk ökning** i Event Hubs automatiskt skalar upp genom att öka antalet dataflödesenheter, för användning behov. Öka genomflödesenheter förhindrar begränsningsscenarier där:

* Ingående datataxa överskrida set-dataflödesenheter.
* Datataxa utgående begäran överskrider set-dataflödesenheter.

Händelsehubbtjänsten ökar dataflödet när belastningen ökar den minsta överskrids utan några förfrågningar som misslyckas med ServerBusy fel.

## <a name="how-auto-inflate-works"></a>Så här fungerar automatisk ökning

Trafik för Event Hubs styrs av [genomflödesenheter](event-hubs-features.md#throughput-units). En genomflödesenhet kan 1 MB per sekund för ingångshändelser och två gånger det beloppet utgående data. Standard händelsehubbar kan konfigureras med 1 – 20 dataflödesenheter. Automatisk ökning kan du börja i liten skala med de minsta nödvändiga dataflödesenheter som du väljer. Funktionen skalas sedan automatiskt till den maximala gränsen på dataflödesenheter som du behöver, beroende på ökade trafiken. Automatisk ökning ger följande fördelar:

- En effektiv skalning mekanism för att börja i liten skala och skala upp när företaget växer.
- Skala automatiskt till den angivna övre gränsen utan begränsning av problem.
- Mer kontroll över skalning, eftersom du styra när och hur mycket skala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivera automatisk ökning på ett namnområde

Du kan aktivera eller inaktivera automatisk ökning på ett namnområde för Event Hubs med hjälp av någon av följande metoder:

- Den [Azure-portalen](https://portal.azure.com).
- En [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Aktivera automatisk ökning via portalen


#### <a name="enable-at-the-time-of-creation"></a>Aktivera när den skapas 
Du kan aktivera funktionen för automatisk ökning **när du skapar ett namnområde för Event Hubs**:
 
![Aktivera automatisk ökning vid skapande av händelsehubb i tid](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Det här alternativet, kan du börja i liten skala med dina dataflödesenheter och skala upp som kräver din användning. Den övre gränsen för inflationen påverkar direkt inte prissättning, beroende på antalet dataflödesenheter som används per timme.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Aktivera automatisk ökning för en befintlig händelsehubb
Du kan också aktivera funktionen för automatisk ökning och ändra dess inställningar med hjälp av följande anvisningar: 
 
1. På den **Event Hubs Namespace** väljer **inaktiverad** under **automatisk ökning genomflödesenheter**.  

    ![Välj dataflödesenheter på sidan för Event Hubs-namnområde](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. I den **Skalningsinställningar** markerar du kryssrutan för **aktivera** (om funktionen för automatisk skalning inte aktiverad).

    ![Välj Aktivera](./media/event-hubs-auto-inflate/scale-settings.png)
3. Ange den **maximala** antal dataflödesenheter eller Använd rullningslisten för att ange ett värde. 
4. (valfritt) Uppdatera den **minsta** antal dataflödesenheter överst i den här sidan. 


> [!NOTE]
> När du tillämpar den automatisk ökning av konfigurationen för att öka antalet throughput units, Event Hubs-tjänsten genererar diagnostikloggar som tillhandahåller information om när och varför det ökade dataflödet. Aktivera Diagnostisk loggning för en händelsehubb **diagnostikinställningar** på menyn till vänster på sidan Händelsehubb i Azure-portalen. Mer information finns i [registrerat diagnostikloggar för en Azure-händelsehubb](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Aktivera automatisk ökning med en Azure Resource Manager-mall

Du kan aktivera automatisk ökning under en malldistribution för Azure Resource Manager. Till exempel den `isAutoInflateEnabled` egenskap **SANT** och ange `maximumThroughputUnits` till 10. Exempel:

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

Läs den fullständiga mallen, den [skapa Event Hubs-namnområdet och aktivera ökning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) mall på GitHub.


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)

