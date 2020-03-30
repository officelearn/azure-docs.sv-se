---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477280"
---
Posterna i `deploymentconfig.json` dokumentöversikten till parametrarna för [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för metoden:

| JSON-enhet | Parametern Metod | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | Ej tillämpligt | Beräkningsmålet. För lokala mål måste `local`värdet vara . |
| `port` | `port` | Den lokala port som tjänstens HTTP-slutpunkt ska exponeras på. |

Detta JSON är ett exempel distributionskonfiguration för användning med CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
