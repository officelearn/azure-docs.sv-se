---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486091"
---
Posterna i `deploymentconfig.json` dokumentöversikten till parametrarna för [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för metoden:

| JSON-enhet | Parametern Metod | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | Ej tillämpligt | Beräkningsmålet. För AKS måste värdet `aks`vara . |
| `autoScaler` | Ej tillämpligt | Innehåller konfigurationselement för automatisk skalning. Se tabellen för automatisk skalning. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Om du vill aktivera automatisk skalning för webbtjänsten. `numReplicas`  = Om `0` `True`, ; annars. `False` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Det minsta antalet behållare som ska användas när den här webbtjänsten automatiskt ska kalibreras. Standard, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Det maximala antalet behållare som ska användas när den här webbtjänsten automatiskt ska kalibreras. Standard, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hur ofta autoskalningsapparaten försöker skala den här webbtjänsten. Standard, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Målutnyttjandet (i procent av 100) som den automatiska skalanvändaren ska försöka underhålla för den här webbtjänsten. Standard, `70`. |
| `dataCollection` | Ej tillämpligt | Innehåller konfigurationselement för datainsamling. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Om du vill aktivera insamling av modelldata för webbtjänsten. Standard, `False`. |
| `authEnabled` | `auth_enabled` | Om nyckelautentisering för webbtjänsten ska aktiveras eller inte. Båda `tokenAuthEnabled` `authEnabled` och `True`kan inte vara . Standard, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Om tokenautentisering för webbtjänsten ska aktiveras eller inte. Båda `tokenAuthEnabled` `authEnabled` och `True`kan inte vara . Standard, `False`. |
| `containerResourceRequirements` | Ej tillämpligt | Behållare för CPU- och minnesentiteter. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Antalet CPU-kärnor som ska allokeras för den här webbtjänsten. Standardvärden`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) som ska allokeras för den här webbtjänsten. Standard`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Om application insights-loggning ska aktiveras för webbtjänsten. Standard, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | En timeout för att framtvinga för bedömning av anrop till webbtjänsten. Standard, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Den maximala samtidiga begäranden per nod för den här webbtjänsten. Standard, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Den maximala tiden en begäran kommer att stanna i dig-kön (i millisekunder) innan ett 503-fel returneras. Standard, `500`. |
| `numReplicas` | `num_replicas` | Antalet behållare som ska allokeras för den här webbtjänsten. Inget standardvärde. Om den här parametern inte är inställd aktiveras den automatiska skalningsapparaten som standard. |
| `keys` | Ej tillämpligt | Innehåller konfigurationselement för nycklar. |
| &emsp;&emsp;`primaryKey` | `primary_key` | En primär autentiseringsnyckel som ska användas för den här webbtjänsten |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | En sekundär autentiseringsnyckel som ska användas för den här webbtjänsten |
| `gpuCores` | `gpu_cores` | Antalet GPU-kärnor (replik per behållare) som ska allokeras för den här webbtjänsten. Standard är 1. Stöder endast heltalsvärden. |
| `livenessProbeRequirements` | Ej tillämpligt | Innehåller konfigurationselement för krav på livenessavsökning. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Hur ofta (i sekunder) för att utföra liveness sonden. Standard till 10 sekunder. Minimivärdet är 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Antal sekunder efter att behållaren har startat innan liveness-avsökningar initieras. Standardvärdet till 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Antal sekunder efter vilka liveness-sonden tar time out. Standardvärdet till 2 sekunder. Minimivärdet är 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minsta på varandra följande framgångar för liveness sonden att anses framgångsrik efter att ha misslyckats. Standard till 1. Minimivärdet är 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | När en pod startar och liveness-sonden misslyckas, kommer Kubernetes att försöka felStrösiska gånger innan de ger upp. Standard till 3. Minimivärdet är 1. |
| `namespace` | `namespace` | Det Kubernetes-namnområde som webbtjänsten distribueras till. Upp till 63 gemener alfanumeriska ("a'-'z", '0'-'9') och bindestreck ('-') tecken. Det första och sista antalet tecken kan inte vara bindestreck. |

Följande JSON är en exempeldistributionskonfiguration som kan användas med CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
