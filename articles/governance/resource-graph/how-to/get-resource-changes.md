---
title: Hämta resursändringar
description: Förstå hur du hittar när en resurs har ändrats och hämtar en lista över de egenskaper som har ändrats.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980298"
---
# <a name="get-resource-changes"></a>Hämta resursändringar

Resurser ändras genom daglig användning, omkonfiguration och till och med omdistribution.
Ändringen kan komma från en person eller en automatiserad process. De flesta ändringar är av design, men ibland är det inte. Med de senaste 14 dagarna i ändrings historiken kan du med Azure Resource Graph:

- se när ändringar har identifierats på en Azure Resource Manager-egenskap
- se vilka egenskaper som ändrats som en del av ändringshändelsen.

Ändrings identifiering och information är värdefull i följande exempel scenarier:

- Under incident hantering kan du förstå _potentiellt_ relaterade ändringar. Fråga efter ändrings händelser under en angiven tids period och utvärdera ändrings informationen.
- Upprätthålla en databas för konfigurations hantering, som kallas en CMDB, uppdaterad. I stället för att uppdatera alla resurser och deras fullständiga egenskaps uppsättningar enligt en schemalagd frekvens får du bara det som ändrades.
- Förstå vilka andra egenskaper som kan ha ändrats när en resurs ändrade kompatibilitetstillstånd. Utvärdering av dessa ytterligare egenskaper kan ge insikter om andra egenskaper som kan behöva hanteras via en Azure Policy-definition.

Den här artikeln visar hur du samlar in den här informationen via resurs Diagramets SDK. Information om hur du kan se den här informationen i Azure Portal finns i Azure Policys [ändrings historik](../../policy/how-to/determine-non-compliance.md#change-history-preview) eller [ändrings historik](../../../azure-monitor/platform/activity-log-view.md#azure-portal)för Azure aktivitets loggen.

> [!NOTE]
> Ändrings informationen i resurs diagrammet är för Resource Manager-egenskaper. Information om hur du spårar ändringar i en virtuell dator finns i Azure Automation [ändrings spårning](../../../automation/automation-change-tracking.md) eller Azure policys [gäst konfiguration för virtuella datorer](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Ändrings historiken i Azure Resource Graph finns i en offentlig för hands version.

## <a name="find-when-changes-were-detected"></a>Sök när ändringar upptäcktes

Det första steget i att se vad som har ändrats på en resurs är att hitta ändrings händelserna som är relaterade till resursen inom ett tidsintervall. Det här steget görs genom **resourceChanges** REST-slutpunkten.

**ResourceChanges** -slutpunkten kräver två parametrar i begär ande texten:

- **resourceId**: Azure-resursen att söka efter ändringar på.
- **intervall**: En egenskap med _Start_ - _och slutdatum för_ när du ska söka efter en ändrings händelse med **Zulu Time Zone (Z)** .

Exempel-begärandetexten:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Med ovanstående begär ande text är REST API-URI: n för **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Svaret ser ut ungefär som i det här exemplet:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Varje identifierad ändrings händelse för **resourceId** har en **changeId** som är unik för den resursen. **ChangeId** -strängen kan ibland innehålla andra egenskaper, men den garanterar bara att vara unik. Ändrings posten innehåller de gånger som tidigare och efter ögonblicks bilder togs.
Ändrings händelsen inträffade vid något tillfälle i det här fönstret.

## <a name="see-what-properties-changed"></a>Se vilka egenskaper som har ändrats

Med **changeId** från **resourceChanges** -slutpunkten används **resourceChangeDetails** REST-slutpunkten för att hämta information om ändrings händelsen.

**ResourceChangeDetails** -slutpunkten kräver två parametrar i begär ande texten:

- **resourceId**: Azure-resursen att söka efter ändringar på.
- **changeId**: Den unika ändrings händelsen för **resourceId** som samlats in från **resourceChanges**.

Exempel-begärandetexten:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Med ovanstående begär ande text är REST API-URI: n för **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Svaret ser ut ungefär som i det här exemplet:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** och **afterSnapshot** var och en anger tiden då ögonblicks bilden togs och egenskaperna vid denna tidpunkt. Ändringen skedde mellan dessa ögonblicks bilder. I exemplet ovan kan vi se att egenskapen som ändrades var **supportsHttpsTrafficOnly**.

Jämför resultaten program mässigt genom att jämföra **innehålls** delen av varje ögonblicks bild för att fastställa skillnaden. Om du jämför hela ögonblicks bilden visas alltid **tidsstämpeln** som en differens trots att den förväntas.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig att [utforska resurser](../concepts/explore-resources.md).