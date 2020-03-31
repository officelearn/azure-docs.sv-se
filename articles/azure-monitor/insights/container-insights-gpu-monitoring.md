---
title: Konfigurera GPU-övervakning med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan konfigurera övervakning av Kubernetes-kluster med NVIDIA- och AMD GPU-aktiverade noder med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373315"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurera GPU-övervakning med Azure Monitor för behållare

Från och med agentversion *ciprod03022019*stöder Azure-övervakaren för behållare integrerad agent nu övervakning av GPU-användning (grafiska bearbetningsenheter) på GPU-medvetna Kubernetes-klusternoder och övervakar poddar/behållare som begär och använder GPU-resurser.

## <a name="supported-gpu-vendors"></a>GPU-leverantörer som stöds

Azure Monitor for Containers stöder övervakning av GPU-kluster från följande GPU-leverantörer:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor for containers startar automatiskt övervakning av GPU-användning på noder och GPU som begär poddar och arbetsbelastningar genom att samla in följande mått med 60sec-intervall och lagra dem i **insightmetrics-tabellen:**

|Måttnamn |Måttdimension (taggar) |Beskrivning |
|------------|------------------------|------------|
|behållareGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Procentandel av tiden under den senaste exempelperioden (60 sekunder) under vilken GPU var upptagen /aktivt bearbetning för en behållare. Arbetscykeln är ett tal mellan 1 och 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Varje behållare kan ange gränser som en eller flera GPU:er. Det går inte att begära eller begränsa en bråkdel av en GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Varje behållare kan begära en eller flera GPU:er. Det går inte att begära eller begränsa en bråkdel av en GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Mängden GPU-minne i byte som är tillgängligt att använda för en viss behållare. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Mängden GPU-minne i byte som används av en viss behållare. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Antal GPU:er i en nod som kan användas av Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Totalt antal GPU:er i en nod. |

## <a name="gpu-performance-charts"></a>Prestandadiagram för GPU 

Azure Monitor för behållare innehåller förkonfigurerade diagram för mått som anges tidigare i tabellen som en GPU-arbetsbok för varje kluster. Du hittar GPU-arbetsboken **Nod GPU** direkt från ett AKS-kluster genom att välja **Arbetsböcker** från den vänstra rutan och i listrutan **Visa arbetsböcker** i insikten.

## <a name="next-steps"></a>Nästa steg

- Se [Använda GPU:er för beräkningsintensiva arbetsbelastningar på Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) för att lära dig hur du distribuerar ett AKS-kluster som innehåller GPU-aktiverade noder.

- Läs mer om [GPU-optimerade virtuella datorer i Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Granska [GPU-stöd i Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) om du vill veta mer om Kubernetes experimentella stöd för hantering av GPU:er i en eller flera noder i ett kluster.