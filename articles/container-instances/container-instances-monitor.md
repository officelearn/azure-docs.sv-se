---
title: Övervaka behållare i Azure Container Instances
description: Information om hur du övervakar användningen av beräkningsresurser som CPU och minne genom behållare i Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: c4834d7f9c63735fe08ddc8d023ad9bf34b891cc
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822508"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Övervaka behållarresurser i Azure Container Instances

Azure Monitor ger inblick i de beräkningsresurser som används av containerinstanserna. Använd Azure Monitor till att spåra CPU- och minnesanvändningen för containergrupper och deras containrar. Dessa resursanvändningsdata hjälper dig att avgöra de bästa inställningarna för CPU och minne för containergrupper.

Det här dokumentet beskriver insamlingen av CPU- och minnesanvändning för containerinstanser med både Azure Portal och Azure CLI.

> [!IMPORTANT]
> För tillfället är bara resursanvändningsstatistik tillgänglig för Linux-containrar.
>

## <a name="available-metrics"></a>Tillgängliga mått

Med Azure Monitor får du användningsmått för både **CPU** och **minne** för Azure Container Instances. Båda måtten tillgängliga för en containergrupp och för enskilda containrar.

CPU-mått uttrycks i **millicores**. En millicore är en tusendels processorkärna så 500 millicores (eller 500 m) representerar 50 % användning av en processorkärna.

Minnesmått uttrycks i **byte**.

## <a name="get-metrics---azure-portal"></a>Hämta mått – Azure Portal

När en containergrupp skapas är Azure Monitor-data tillgängliga i Azure Portal. Välj resursgruppen och sedan containergruppen om du vill visa måtten för en containergrupp. Här kan du se förskapade diagram för både CPU- och minnesanvändning.

![dubbelt diagram][dual-chart]

Om du har en containergrupp som innehåller flera containrar använder du en [dimension][monitor-dimension] för att presentera mått för varje enskild container. Utför följande steg för att skapa ett diagram med enskilda containrar:

1. Välj **Övervakare** i den vänstra navigeringsmenyn.
2. Välj en containergrupp och ett mått (CPU eller minne).
3. Välj den gröna dimensionsknappen och välj **Containernamn**.

![dimension][dimension]

## <a name="get-metrics---azure-cli"></a>Hämta mått – Azure CLI

CPU- och minnesanvändning för Container Instances kan också samlas in med hjälp av Azure CLI. Hämta först containergruppens ID med följande kommando. Ersätt `<resource-group>` med resursgruppens namn och `<container-group>` med namnet på containergruppen.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Använd följande kommando för att hämta **CPU**-användningsstatistik.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Och följande kommando för att hämta användningsstatistik för **minne**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

För en grupp med flera containrar kan `containerName` dimensionen läggas till för att returnera dessa data per container.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-övervakning i [översikten över Azure övervakning][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-collection.md#multi-dimensional-metrics
