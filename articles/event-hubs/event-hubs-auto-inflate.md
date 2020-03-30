---
title: Skala automatiskt upp dataflödesenheter – Azure Event Hubs | Microsoft-dokument
description: Aktivera Automatisk uppblåsning på ett namnområde för att automatiskt skala upp dataflödesenheter.
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
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264468"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Skala automatiskt upp Azure Event Hubs-dataflödesenheter
Azure Event Hubs är en mycket skalbar dataströmningsplattform. Därför ökar användningen av eventhubbar ofta när tjänsten har startats. Sådan användning kräver att öka de förutbestämda [dataflödesenheterna](event-hubs-scalability.md#throughput-units) för att skala eventhubbar och hantera större överföringshastigheter. Funktionen **För automatisk uppblåsning** i Event Hubs skalas automatiskt upp genom att öka antalet dataflödesenheter för att uppfylla användningsbehoven. Genomströmningsenheter förhindrar begränsningsscenarier, där:

* Datainträngningshastigheten överskrider inställda dataflödesenheter.
* Datautgående begäranhetsfrekvens överskrider angivna dataflödesenheter.

Event Hubs-tjänsten ökar dataflödet när belastningen ökar utöver minimitröskeln, utan att några begäranden misslyckas med ServerBusy-fel.

## <a name="how-auto-inflate-works"></a>Så här fungerar Automatisk uppblåsning

Event Hubs-trafiken styrs av [dataflödesenheter](event-hubs-scalability.md#throughput-units). En enda dataflödesenhet tillåter 1 MB per sekund ingående och dubbelt så mycket utgående. Standardhändelsehubbar kan konfigureras med 1-20 dataflödesenheter. Automatisk uppblåsning gör att du kan börja i liten skala med de minsta nödvändiga dataflödesenheterna du väljer. Funktionen skalas sedan automatiskt till den maximala gränsen för dataflödesenheter du behöver, beroende på ökningen av din trafik. Automatisk uppblåsning ger följande fördelar:

- En effektiv skalningsmekanism för att börja i liten skala och skala upp när du växer.
- Skala automatiskt till den angivna övre gränsen utan begränsningsproblem.
- Mer kontroll över skalning, eftersom du styr när och hur mycket du ska skala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivera Automatisk uppblåsning på ett namnområde

Du kan aktivera eller inaktivera Automatisk uppblåsning på ett namnområde för händelsehubbar på standardnivå med någon av följande metoder:

- [Azure-portalen](https://portal.azure.com).
- En [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Namnområden för grundläggande nivåhändelsehubbar stöder inte automatisk uppblåsning.

### <a name="enable-auto-inflate-through-the-portal"></a>Aktivera Automatisk uppblåsning via portalen


#### <a name="enable-at-the-time-of-creation"></a>Aktivera vid tidpunkten för skapandet 
Du kan aktivera funktionen Blås upp **automatiskt när du skapar ett namnområde för händelsehubbar:**
 
![Aktivera automatisk uppblåsning när händelsehubben skapas](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Med det här alternativet aktiverat kan du börja i liten skala med dataflödesenheterna och skala upp när användningsbehoven ökar. Den övre gränsen för inflation påverkar inte omedelbart prissättningen, vilket beror på antalet dataflödesenheter som används per timme.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Aktivera automatisk uppblåsning för ett befintligt händelsenav
Du kan också aktivera funktionen Blåupplåsning automatiskt och ändra dess inställningar med hjälp av följande instruktioner: 
 
1. På sidan **Namnrymdsnamnområde för händelsehubbar** väljer du **Inaktiverad** under **Automatisk uppblåsning av dataflödesenheter**.  

    ![Välj dataflödesenheter på namnområdessidan för händelsehubbar](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Markera kryssrutan för **Aktivera** på sidan **Skalningsinställningar** (om funktionen för automatisk skalning inte var aktiverad).

    ![Välj Aktivera](./media/event-hubs-auto-inflate/scale-settings.png)
3. Ange det **maximala** antalet dataflödesenheter eller använd rullningslisten för att ange värdet. 
4. (valfritt) Uppdatera det **minsta** antalet dataflödesenheter högst upp på den här sidan. 


> [!NOTE]
> När du använder konfigurationen för automatisk uppblåsning för att öka dataflödesenheterna avger tjänsten Event Hubs diagnostikloggar som ger dig information om varför och när dataflödet ökade. Om du vill aktivera diagnostikloggning för en händelsehubb väljer du **Diagnostikinställningar** på den vänstra menyn på sidan Event Hub i Azure-portalen. Mer information finns i [Konfigurera diagnostikloggar för en Azure-händelsehubb](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Aktivera Automatisk uppblåsning med hjälp av en Azure Resource Manager-mall

Du kan aktivera Automatisk uppblåsning under en Azure Resource Manager-malldistribution. Ange till exempel `isAutoInflateEnabled` egenskapen till `maximumThroughputUnits` **true** och ange 10. Ett exempel:

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

Den fullständiga mallen finns i [namnområdet Skapa händelsehubbar och aktivera uppblåsningsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) på GitHub.


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)

