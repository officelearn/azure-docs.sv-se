---
title: Hämta resursändringar
description: Lär dig hur du hittar när en resurs har ändrats och hämta en lista över de egenskaper som ändrats.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 81cb5e62c8abc93aa5defb690628c7178fd2a869
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142544"
---
# <a name="get-resource-changes"></a>Hämta resursändringar

Resurser får ändra via loppet av daglig användning, omkonfiguration och även omdistributionen.
Ändra kan komma från en person eller av en automatiserad process. De flesta ändringen är avsiktligt, men ibland är det inte. Med de senaste 14 dagarna ändringshistoria kan Azure Resource Graph du:

- se när ändringar har identifierats på en Azure Resource Manager-egenskap
- se vilka egenskaper som ändrats som en del av ändringshändelsen.

Identifiering av ändring av och information är värdefull för följande exempelscenarier:

- Under incidenthantering för att förstå _potentiellt_ relaterade ändringar. Fråga efter händelser under en viss tidsperiod och utvärdera information om ändringar.
- Att hålla en Konfigurationshanteringsdatabas kallas en CMDB uppdaterad. I stället för att uppdatera alla resurser och deras fullständiga egenskapsuppsättningar enligt en frekvens som är schemalagd att bara hämta vad som ändrats.
- Förstå vilka andra egenskaper kan ändras när en resurs ändras kompatibilitetsstatus. Utvärdering av dessa ytterligare egenskaper kan ge insikter om andra egenskaper som kan behöva hanteras via en Azure Policy definition.

Den här artikeln visar hur du samlar in uppgifterna via Resource Graph SDK. Den här informationen i Azure-portalen finns i Azure Policy [ändringshistorik](../../policy/how-to/determine-non-compliance.md#change-history-preview) eller Azure-aktivitetsloggen [ändringshistorik](../../../azure-monitor/platform/activity-logs-overview.md#view-change-history).

> [!NOTE]
> Information om ändringar i resurs-diagrammet är för Resource Manager-egenskaper. Spåra ändringar i en virtuell dator finns i Azure Automation [ändringsspårning](../../../automation/automation-change-tracking.md) eller Azure Policy [gäst-konfigurationen för virtuella datorer](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Ändra historik i Azure Resource Graph är i offentlig förhandsversion.

## <a name="find-when-changes-were-detected"></a>Hitta när ändringar har upptäckts

Det första steget i att se vad som ändrats på en resurs är att hitta ändringen händelser relaterade till resursen i en tidsperiod. Det här steget gör du genom de **resourceChanges** REST-slutpunkt.

Den **resourceChanges** slutpunkten kräver två parametrar i begärandetexten:

- **resourceId**: Azure-resursen ska sökas efter ändringar.
- **Intervall**: En egenskap med _starta_ och _slutet_ datum för när du ska söka efter en ändring händelse med hjälp av den **Zulu tidszon (Z)**.

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

Med ovanstående begärandetexten, REST API-URI för **resourceChanges** är:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Svaret liknar det här exemplet:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
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

Varje identifierad Ändringshändelse för den **resourceId** har en **changeId** som är unik för den här resursen. Medan den **changeId** sträng kan ibland innehålla andra egenskaper, det har bara säkert vara unikt. Ändra posten innehåller tiderna som den före och efter ögonblicksbilder som utförts.
Ändra händelsen inträffade vid en tidpunkt i det här fönstret.

## <a name="see-what-properties-changed"></a>Se vad som ändrats egenskaper

Med den **changeId** från den **resourceChanges** slutpunkten, den **resourceChangeDetails** REST-slutpunkt används sedan för att hämta information om ändringshändelsen.

Den **resourceChangeDetails** slutpunkten kräver två parametrar i begärandetexten:

- **resourceId**: Azure-resursen ska sökas efter ändringar.
- **changeId**: Unikt ändringshändelsen för den **resourceId** samlats in från **resourceChanges**.

Exempel-begärandetexten:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

Med ovanstående begärandetexten, REST API-URI för **resourceChangeDetails** är:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Svaret liknar det här exemplet:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** och **afterSnapshot** var och ge den tid som ögonblicksbilden togs och egenskaper vid den tidpunkten. Ändringen hände någon gång mellan de här ögonblicksbilderna. Studera exemplet ovan kan vi se att den egenskap som ändrades var **supportsHttpsTrafficOnly**.

Om du vill jämföra resultaten programmässigt, jämför den **innehåll** delen av varje ögonblicksbild för att avgöra skillnaden. Om du jämför hela ögonblicksbilden, om den **tidsstämpel** visar alltid som en skillnad trots som förväntat.

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md).
- Se avancerade använder i [avancerade frågor](../samples/advanced.md).
- Lär dig hur du [Utforska resurser](../concepts/explore-resources.md).
