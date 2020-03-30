---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485985"
---
Posterna i `deploymentconfig.json` dokumentöversikten till parametrarna för [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för metoden:

| JSON-enhet | Parametern Metod | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | Ej tillämpligt | Beräkningsmålet. För ACI måste värdet `ACI`vara . |
| `containerResourceRequirements` | Ej tillämpligt | Behållare för CPU- och minnesentiteter. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Antalet CPU-kärnor att allokera. Standardvärden`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) som ska allokeras för den här webbtjänsten. Standard`0.5` |
| `location` | `location` | Azure-regionen som den här webbtjänsten ska distribuera till. Om det inte anges kommer arbetsytan att användas. Mer information om tillgängliga regioner hittar du här: [ACI-regioner](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Om du vill aktivera autentisering för den här webbtjänsten. Standardvärden till False |
| `sslEnabled` | `ssl_enabled` | Om SSL ska aktiveras för den här webbtjänsten. Standardvärdet till Falskt. |
| `appInsightsEnabled` | `enable_app_insights` | Om AppInsights ska aktiveras för den här webbtjänsten. Standardvärden till False |
| `sslCertificate` | `ssl_cert_pem_file` | Cert-filen behövs om SSL är aktiverat |
| `sslKey` | `ssl_key_pem_file` | Nyckelfilen som behövs om SSL är aktiverat |
| `cname` | `ssl_cname` | C-namnet för om SSL är aktiverat |
| `dnsNameLabel` | `dns_name_label` | Dns-namnetiketten för poängslutpunkten. Om inget anges genereras en unik dns-namnetikett för poängslutpunkten. |

Följande JSON är en exempeldistributionskonfiguration som kan användas med CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```