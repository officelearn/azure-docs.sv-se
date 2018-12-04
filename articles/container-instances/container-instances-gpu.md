---
title: Distribuera GPU-aktiverade Azure container instances
description: Lär dig hur du distribuerar Azure container instances ska köras på GPU-resurser.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: d0278a58bfad6f2a4a964ef29fbff8a111b475b5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856846"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Distribuera behållarinstanser som använder GPU-resurser

Om du vill köra vissa beräkningsintensiva arbetsbelastningar på Azure Container Instances, distribuerar du din behållargrupper med *GPU resurser*. Behållarinstanserna har åtkomst till en eller flera NVIDIA Tesla-grafikprocessorer vid körning av behållararbetsbelastningar som CUDA och deep learning-program.

Som du ser i den här artikeln kan du kan lägga till GPU-resurser när du distribuerar en behållargrupp med en [YAML-fil](container-instances-multi-container-yaml.md) eller [Resource Manager-mall](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Den här funktionen finns för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar i förhandsversionen

I förhandsversion begränsningar gäller följande när du använder GPU-resurser i grupper med behållare. 

**Regioner som stöds**:

* Östra USA (eastus)
* Västra USA 2 (västra USA 2)
* Södra centrala USA (usasödracentrala)
* Västeuropa (westeurope)
* Nordeuropa (europanorra)
* Östasien (asienöstra)
* Centrala Indien (indiencentrala)

Stöd kommer att läggas till för ytterligare regioner över tid.

**OS-typer som stöds**: endast Linux

**Ytterligare begränsningar**: GPU-resurser kan inte användas när du distribuerar en behållargrupp i en [virtuellt nätverk](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Om GPU-resurser

### <a name="count-and-sku"></a>Antal och SKU

Om du vill använda GPU: er i en behållarinstans, ange en *GPU resource* med följande information:

* **Antal** -antalet GPU: er: **1**, **2**, eller **4**.
* **SKU** -GPU-SKU: **K80**, **P100**, eller **V100**. Varje SKU som mappar till NVIDIA Tesla GPU i ett familjerna Azure GPU-aktiverad virtuell dator:

  | SKU | VM-serie |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

### <a name="cpu-and-memory"></a>CPU och minne

Ange Processorn och minnesresurser passar arbetsbelastningen, upp till de högsta värden som visas i följande tabell när du distribuerar GPU-resurser. Dessa värden är för närvarande är större än processor- och minnesgränser i container instances utan GPU-resurser.  

| GPU-SKU | GPU-antal | Processor |  Minne (GB) |
| --- | --- | --- | --- |
| K80 | 1 | 6 | 56 |
| K80 | 2 | 12 | 112 |
| K80 | 4 | 24 | 224 |
| P100 | 1 | 6 | 112 |
| P100 | 2 | 12 | 224 |
| P100 | 4 | 24 | 448 |
| V100 | 1 | 6 | 112 |
| V100 | 2 | 12 | 224 |
| V100 | 4 | 24 | 448 |

### <a name="things-to-know"></a>Saker att känna till

* **Distributionstiden** -skapandet av en behållargrupp som innehåller GPU resurser tar upp till **8 – 10 minuter**. Detta beror på den extra tid att etablera och konfigurera en GPU VM i Azure. 

* **Priser** – påminner om behållargrupper utan GPU-resurser, Azure-fakturor för resurser som används över den *varaktighet* för en behållargrupp med GPU-resurser. Varaktigheten beräknas från tidpunkten då att hämta din första behållares avbildning till dess att behållargruppen avslutas. Det inkluderar inte tiden för att distribuera behållargruppen.

  Priserna är högre för behållargrupper med GPU-resurser än för behållargrupper utan. Se [prisinformation](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA drivrutiner** – Container instances med GPU-resurser är företablerade med NVIDIA CUDA-drivrutiner och behållarkörningar, så du kan använda behållaravbildningar som har utvecklats för CUDA-arbetsbelastningar.

## <a name="yaml-example"></a>YAML-exempel

Kopiera följande YAML till en ny fil med namnet *gpu distribuera aci.yaml*, spara filen. Den här YAML skapar en behållargrupp med namnet *gpucontainergroup* att ange en behållarinstans med en K80 GPU. Instansen körs ett CUDA vektor tillägg-exempelprogram. Resursbegäranden är tillräckliga för att köra arbetsbelastningen.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
    osType: Linux
  restartPolicy: OnFailure
```

Distribuera behållargrupp med den [az container skapa] [ az-container-create] kommando och ange namnet på YAML-filen för den `--file` parametern. Du måste ange namnet på en resursgrupp och en plats för behållargruppen som *eastus* som har stöd för GPU-resurser.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Distributionen tar normalt flera minuter för att slutföras. Sedan behållaren startas och körs en CUDA-vektor additionen. Kör den [az behållarloggarna] [ az-container-logs] kommando för att visa loggutdata:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Resultat:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Mallexemplet för Resource Manager

Börja med att skapa en fil med namnet `gpudeploy.json`, kopiera följande JSON till den. Det här exemplet distribuerar en behållarinstans med en V100-GPU som kör en [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) utbildningsjobb mot den [MNIST datauppsättning](http://yann.lecun.com/exdb/mnist/). Resursbegäranden är tillräckliga för att köra arbetsbelastningen.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Distribuera mallen med den [az group deployment skapa] [ az-group-deployment-create] kommando. Du måste ange namnet på en resursgrupp som har skapats i en region som *eastus* som har stöd för GPU-resurser.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Distributionen tar normalt flera minuter för att slutföras. Sedan behållaren startas och körs TensorFlow-jobb. Kör den [az behållarloggarna] [ az-container-logs] kommando för att visa loggutdata:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Resultat:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom med GPU-resurser kan vara dyra, kontrollerar du att dina behållare inte kör oväntat under långa perioder. Övervaka behållare i Azure-portalen eller kontrollera status för en behållargrupp med den [az container show] [ az-container-show] kommando. Exempel:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

När du är klar du arbetar med behållarinstanserna skapade, ta bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar en behållare med en [YAML-fil](container-instances-multi-container-yaml.md) eller [Resource Manager-mall](container-instances-multi-container-group.md).
* Läs mer om [GPU för VM-storlekar optimerade](../virtual-machines/linux/sizes-gpu.md) i Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create