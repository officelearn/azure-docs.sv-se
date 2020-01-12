---
title: Distribuera på dedikerade värdar
description: Använd dedikerade värdar för att uppnå sann isolering på värdnivå för dina arbets belastningar
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903761"
---
# <a name="deploy-on-dedicated-hosts"></a>Distribuera på dedikerade värdar

"Dedikerad" är en Azure Container Instances-SKU (ACI) som tillhandahåller en isolerad och dedikerad beräknings miljö för att köra behållare på ett säkert sätt. Genom att använda dedikerade SKU-resultat i varje behållar grupp som har en dedikerad fysisk server i ett Azure-datacenter kan du se till att fullständig arbets belastnings isolering uppfyller organisationens krav på säkerhet och efterlevnad. 

Den dedikerade SKU: n är lämplig för behållar arbets belastningar som kräver arbets belastnings isolering från ett fysiskt Server perspektiv.

## <a name="using-the-dedicated-sku"></a>Använda dedikerad SKU

> [!IMPORTANT]
> Användning av dedikerade SKU är bara tillgänglig i den senaste API-versionen (2019-12-01) som för närvarande är distribuerad. Ange den här API-versionen i distributions mal len. Dessutom är standard gränsen för alla prenumerationer som använder den dedikerade SKU: n 0. Om du vill använda den här SKU: n för distributioner av produktions behållare skapar du ett [Azure-supportbegäran][azure-support]

Från och med API version 2019-12-01 finns en "SKU"-egenskap under avsnittet Egenskaper för container grupp i en distributionsmall som krävs för en ACI-distribution. För närvarande kan du använda den här egenskapen som en del av en mall för Azure Resource Manager distribution för ACI. Du kan lära dig mer om att distribuera ACI-resurser med en mall i [självstudien: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

SKU-egenskapen kan ha ett av följande värden:
* Standard – standard alternativet för ACI distribution, som fortfarande garanterar säkerhet på hypervisor-nivå 
* Dedikerad – används för isolering av arbets belastnings nivåer med dedikerade fysiska värdar för behållar gruppen

## <a name="modify-your-json-deployment-template"></a>Ändra mallen för JSON-distribution

I distributions mal len där behållar grupp resursen har angetts kontrollerar du att `"apiVersion": "2019-12-01",`. Ange `"sku": "Dedicated",`i avsnittet Egenskaper i behållar grupps resursen.

Här är ett exempel-kodfragment för avsnittet resurser i en distributions mall för container grupper som använder den dedikerade SKU: n:

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
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Distribuera din behållar grupp

Om du har skapat och redigerat distributions mal len på Skriv bordet kan du överföra den till din Cloud Shell katalog genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. När distributionen är klar krypteras alla data som är relaterade till den bestående av ACI-tjänsten med den nyckel du angav.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
