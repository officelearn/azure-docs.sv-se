---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800147"
---
Posterna i `deploymentconfig.json` dokument översikt till parametrarna för [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | Ej tillämpligt | Beräkningsmålet. Värdet måste vara `local`för lokala mål. |
| `port` | `port` | Den lokala porten där tjänstens HTTP-slutpunkt ska exponeras. |

Denna JSON är en exempel distributions konfiguration för användning med CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
