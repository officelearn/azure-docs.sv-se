---
title: Azure Monitor-API-pensionering
description: Beskriver indragningen av äldre versioner av OperationalInsights Resource Provider API.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c9a7ba63246f747a132c315e22452ca9c7144448
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058190"
---
# <a name="operationalinsights-api-version-retirement"></a>Borttagning av OperationalInsights-API-version
Microsoft tillhandahåller ett meddelande minst 12 månader i förväg för att dra tillbaka ett API för att utjämna över gången till en nyare/version som stöds. Vi har släppt en ny version (2020-08-01) för **OperationalInsights** Resource Provider-API: er och kommer att dra tillbaka alla tidigare API-versioner den 31 oktober 2023. Eftersom nya funktioner och funktioner och optimeringar bara läggs till i aktuellt API bör du uppgradera till den senaste API-versionen så tidigt som möjligt.

Efter den 31 oktober 2023 kommer Azure Monitor inte längre att ha stöd för tidigare API-versioner än 2020-08-01. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från tidigare versioner att betjänas av Azure Monitor tjänsten fram till den 31 oktober 2023.

## <a name="migration-steps"></a>Migreringsanvisningar
Beroende på vilken konfigurations metod du använder bör du uppdatera den nya versionen i **rest** -begäranden och **Resource Manager-mallar**. Uppdatera API-versionen genom att följa exemplen nedan:

1. REST API begär Anden använder API-versionen i URL: en för begäran. Ersätt den versionen med den senaste versionen (2020-08-01) som du ser i följande exempel.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager mallar använder API-versionen i egenskapen **API version** för resursen. Ersätt den versionen med den senaste versionen (2020-08-01) som du ser i följande exempel.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Nästa steg

- Se [referensen för OperationalInsights-arbetsyte-API: et](https://docs.microsoft.com/rest/api/loganalytics/workspaces).
