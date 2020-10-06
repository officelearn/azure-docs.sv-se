---
title: Azure Monitor-API-pensionering
description: Beskriver indragningen av äldre versioner av OperationalInsights Resource Provider API.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744992"
---
# <a name="operationalinsights-api-version-retirement"></a>Borttagning av OperationalInsights-API-version
Microsoft tillhandahåller ett meddelande minst 12 månader i förväg för att dra tillbaka ett API för att utjämna över gången till en nyare/version som stöds. Vi har släppt en ny version (2020-08-01) för **OperationalInsights** Resource Provider-API: er och kommer att dra tillbaka alla tidigare API-versioner den 31 oktober 2023. Eftersom nya funktioner och funktioner och optimeringar bara läggs till i aktuellt API bör du uppgradera till den senaste API-versionen så tidigt som möjligt.

Efter den 31 oktober 2023 kommer Azure Monitor inte längre att ha stöd för tidigare API-versioner än 2020-08-01. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från tidigare versioner att betjänas av Azure Monitor tjänsten fram till den 31 oktober 2023.

Beroende på vilken konfigurations metod du använder bör du uppdatera den nya versionen i REST-begäranden och Resource Manager-mallar enligt följande exempel:


## <a name="rest"></a>REST
REST API begär Anden använder API-versionen i URL: en för begäran. Ersätt den versionen med den senaste versionen som visas i följande exempel.

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure Resource Manager
Resource Manager-mallar använder API-versionen i egenskapen **API version** för resursen. Ersätt den versionen med den senaste versionen som visas i följande exempel.


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

- Se [referensen för OperationalInsights-API: et](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions).
