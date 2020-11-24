---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561446"
---
Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | NA | Beräkningsmålet. För lokala mål måste värdet vara `local` . |
| `port` | `port` | Den lokala porten där tjänstens HTTP-slutpunkt ska exponeras. |

Denna JSON är en exempel distributions konfiguration för användning med CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```