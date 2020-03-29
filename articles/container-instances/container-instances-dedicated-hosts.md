---
title: Distribuera på dedikerad värd
description: Använd en dedikerad värd för att uppnå verklig isolering på värdnivå för dina Azure Container Instances-arbetsbelastningar
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934153"
---
# <a name="deploy-on-dedicated-hosts"></a>Distribuera på dedikerade värdar

"Dedikerad" är en Azure Container Instances (ACI) sku som tillhandahåller en isolerad och dedikerad beräkningsmiljö för säkert gående behållare. Med hjälp av dedikerade sku-resultaten i varje behållargrupp som har en dedikerad fysisk server i ett Azure-datacenter, vilket säkerställer fullständig arbetsbelastningsisolering för att uppfylla organisationens säkerhets- och efterlevnadskrav. 

Den dedikerade sku är lämplig för behållararbetsbelastningar som kräver arbetsbelastningsisolering från ett fysiskt serverperspektiv.

## <a name="prerequisites"></a>Krav

* Standardgränsen för alla prenumerationer som ska använda den dedikerade sku är 0. Om du vill använda den här sku för distributioner av produktionsbehållare skapar du en [Azure Support-begäran för][azure-support] att öka gränsen.

## <a name="use-the-dedicated-sku"></a>Använd den dedikerade sku

> [!IMPORTANT]
> Att använda den dedikerade sku är endast tillgängligt i den senaste API-versionen (2019-12-01) som för närvarande lanseras. Ange den här API-versionen i distributionsmallen.
>

Från och med API-version 2019-12-01 finns det en `sku` egenskap under avsnittet behållargruppegenskaper i en distributionsmall, som krävs för en ACI-distribution. För närvarande kan du använda den här egenskapen som en del av en Azure Resource Manager-distributionsmall för ACI. Läs mer om hur du distribuerar ACI-resurser med en mall i [självstudiekursen: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Egenskapen `sku` kan ha något av följande värden:
* `Standard`- Standardvalet för ACI-distribution, som fortfarande garanterar säkerhet på hypervisornivå 
* `Dedicated`- används för isolering på arbetsbelastningsnivå med dedikerade fysiska värdar för behållargruppen

## <a name="modify-your-json-deployment-template"></a>Ändra din JSON-distributionsmall

Ändra eller lägg till följande egenskaper i distributionsmallen:
* Under `resources`, `apiVersion` `2012-12-01`inställd på .
* Lägg till en `sku` egenskap med värde `Dedicated`under egenskaperna för behållargrupp.

Här är ett exempel utdrag för resursavsnittet i en distributionsmall för behållargrupp som använder den dedikerade sku:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Följande är en komplett mall som distribuerar en exempelbehållare grupp som kör en enda behållarinstans:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Distribuera din behållargrupp

Om du har skapat och redigerat distributionsmallfilen på skrivbordet kan du överföra den till cloud shell-katalogen genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [az group deployment create.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. En lyckad distribution sker på en dedikerad värd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
