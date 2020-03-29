---
title: Hämta resursändringar
description: Förstå hur du hittar när en resurs har ändrats, hämta en lista över de egenskaper som har ändrats och utvärdera diffs.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873037"
---
# <a name="get-resource-changes"></a>Hämta resursändringar

Resurser ändras genom daglig användning, omkonfiguration och till och med omfördelning.
Förändring kan komma från en individ eller genom en automatiserad process. De flesta förändringar är avsiktligt, men ibland är det inte. Med de senaste 14 dagarnas ändringshistorik kan du med Azure Resource Graph:

- Hitta när ändringar upptäcktes på en Azure Resource Manager-egenskap
- För varje resursändring, se information om egenskapsändring
- Se en fullständig jämförelse av resursen före och efter den identifierade ändringen

Ändringsidentifiering och information är värdefulla för följande exempelscenarier:

- Under incidenthantering för att förstå _potentiellt_ relaterade förändringar. Fråga efter ändringshändelser under ett visst tidsfönster och utvärdera ändringsinformationen.
- Hålla en configuration management-databas, känd som en CMDB, uppdaterad. I stället för att uppdatera alla resurser och deras fullständiga egenskapsuppsättningar på en schemalagd frekvens får du bara det som har ändrats.
- Förstå vilka andra egenskaper som kan ha ändrats när en resurs ändrade efterlevnadstillståndet. Utvärdering av dessa ytterligare egenskaper kan ge insikter om andra egenskaper som kan behöva hanteras via en Azure Policy-definition.

Den här artikeln visar hur du samlar in den här informationen via Resource Graph SDK. Information om hur du ser den här informationen i Azure-portalen finns i Azure Policy [ändringshistorik](../../policy/how-to/determine-non-compliance.md#change-history-preview) eller Azure Activity Log [Change-historik](../../../azure-monitor/platform/activity-log-view.md#azure-portal).
Mer information om ändringar i dina program från infrastrukturlagret hela vägen till programdistribution finns i [Använda programändringsanalys (förhandsversion)](../../../azure-monitor/app/change-analysis.md) i Azure Monitor.

> [!NOTE]
> Ändringsinformation i Resursdiagram är för Resource Manager-egenskaper. Information om hur du spårar ändringar inuti en virtuell dator finns i Azure Automations [ändringsspårning](../../../automation/automation-change-tracking.md) eller Azure-principens [gästkonfiguration för virtuella datorer](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Ändringshistoriken i Azure Resource Graph finns i offentlig förhandsversion.

## <a name="find-detected-change-events-and-view-change-details"></a>Hitta upptäckta ändringshändelser och visa ändringsinformation

Det första steget för att se vad som har ändrats på en resurs är att hitta ändringshändelser som är relaterade till den resursen inom ett tidsfönster. Varje ändringshändelse innehåller också information om vad som har ändrats på resursen. Det här steget görs via **resursenChanges** REST-slutpunkten.

**ResursChanges-slutpunkten** accepterar följande parametrar i begärandetexten:

- **resourceId** \[\]krävs: Azure-resursen att leta efter ändringar på.
- **intervall** \[\]som krävs: En egenskap med _start-_ och _slutdatum_ för när en ändringshändelse ska sökas med **zulu tidszon (Z).**
- **fetchPropertyChanges** (valfritt): En boolesk egenskap som anger om svarsobjektet innehåller egenskapsändringar.

Exempel begäran organ:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Med ovanstående begärandetext är REST API URI för **resourceChanges:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Svaret liknar det här exemplet:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Varje identifierad ändringshändelse för **resourceId** har följande egenskaper:

- **changeId** - Det här värdet är unikt för den resursen. Även om **changeId-strängen** ibland kan innehålla andra egenskaper, är den bara garanterad att vara unik.
- **beforeSnapshot** - Innehåller **snapshotId** och **tidsstämpel** för resursögonblicksbilden som togs innan en ändring upptäcktes.
- **afterSnapshot** - Innehåller **snapshotId** och **tidsstämpel** för resursögonblicksbilden som togs efter att en ändring upptäcktes.
- **changeType** - Beskriver den typ av ändring som har identifierats för hela **ändringsposten mellan föreSnapshot** och **afterSnapshot**. Värdena är: _Skapa,_ _Uppdatera_och _Ta bort_. **EgenskapEn PropertyChanges-egenskapsmatrisen** inkluderas endast när **changeType** är _Update_.
- **propertyChanges** - Den här matrisen med egenskaper beskriver alla resursegenskaper som uppdaterades mellan **föreSnapshot** och **afterSnapshot:**
  - **propertyName** - Namnet på resursegenskapen som har ändrats.
  - **changeCategory** - Beskriver vad som gjorde ändringen. Värdena är: _System_ och _Användare_.
  - **changeType** - Beskriver vilken typ av ändring som har identifierats för den enskilda resursegenskapen.
    Värdena är: _Infoga_, _Uppdatera_, _Ta bort_.
  - **beforeValue** - Värdet för resursegenskapen i **beforeSnapshot**. Visas inte när **changeType** är _Infoga_.
  - **afterValue** - Värdet för resursegenskapen i **afterSnapshot**. Visas inte när **changeType** är _Remove_.

## <a name="compare-resource-changes"></a>Jämföra resursändringar

Med **changeId** från **resursenÄnd-slutpunkten används** **sedan resurschangeDetails** REST-slutpunkten för att hämta före och efter ögonblicksbilder av resursen som har ändrats.

**ResursChangeDetails-slutpunkten** kräver två parametrar i begärandetexten:

- **resourceId**: Azure-resursen att jämföra ändringar på.
- **changeId**: Den unika ändringshändelsen för **resourceId som** samlats in från **resourceChanges**.

Exempel begäran organ:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Med ovanstående begärandetext är REST API URI för **resourceChangeDetails:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Svaret liknar det här exemplet:

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

**beforeSnapshot** och **afterSnapshot** varje ger den tid ögonblicksbilden togs och egenskaperna vid den tidpunkten. Ändringen inträffade någon gång mellan dessa ögonblicksbilder. Om man tittar på exemplet ovan kan vi se att egenskapen som ändrades **stöderHttpsTrafficOnly**.

Om du vill jämföra resultaten använder du egenskapen **Changes** i **resourceChanges** eller **utvärderar** innehållsdelen av varje ögonblicksbild i **resourceChangeDetails** för att fastställa skillnaden. Om du jämför ögonblicksbilderna visas **tidsstämpeln** alltid som en skillnad trots att den förväntas.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Starter-frågor](../samples/starter.md).
- Se avancerade användningsområden i [avancerade frågor](../samples/advanced.md).
- Läs mer om hur du [utforskar resurser](../concepts/explore-resources.md).