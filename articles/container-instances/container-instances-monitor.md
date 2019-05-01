---
title: Övervaka behållare i Azure Container Instances
description: Så här övervakar du förbrukningen av beräkningsresurser som CPU och minne genom dina behållare i Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570936"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Övervaka behållarresurser i Azure Container Instances

[Azure Monitor] [ azure-monitoring] ger insikt i de beräkningsresurser som används av containers-instanser. Den här resursanvändningsdata hjälper dig att avgöra bästa resursinställningarna för din behållargrupper. Azure Monitor innehåller också mått som spårar nätverksaktivitet i container instances.

Det här dokumentet beskriver samlar in Azure Monitor-mått för container instances med både Azure-portalen och Azure CLI.

> [!IMPORTANT]
> Azure Monitor-mått i Azure Container Instances är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Azure Monitor mått är för närvarande endast tillgängliga för Linux-behållare.

## <a name="available-metrics"></a>Tillgängliga mått

Azure Monitor innehåller följande [mätvärden för Azure Container Instances][supported-metrics]. De här måtten är tillgängliga för en behållargrupp och enskilda behållare.

* **CPU-användning** – mätt i **millicores**. En millicore är 1/1000th av en CPU-kärna, så 500 millicores (eller 500 m) representerar 50% användning av CPU-kärna. Aggregeras som **genomsnittlig användning** över alla kärnor.

* **Minnesanvändning** – aggregerade som **genomsnittlig byte**.

* **Network mottagna byte Per sekund** och **nätverk byte skickas Per sekund** – aggregerade som **genomsnittlig byte per sekund**. 

## <a name="get-metrics---azure-portal"></a>Hämta mått – Azure Portal

När en containergrupp skapas är Azure Monitor-data tillgängliga i Azure Portal. Om du vill visa mått för en behållargrupp, går du till den **översikt** för behållargruppen. Här kan du visa diagram som skapats i förväg för var och en av de tillgängliga måtten.

![dubbelt diagram][dual-chart]

I en behållargrupp som innehåller flera behållare använder en [dimension] [ monitor-dimension] att presentera mått av behållare. Utför följande steg för att skapa ett diagram med enskilda containrar:

1. I den **översikt** väljer du något av måttdiagram, till exempel **CPU**. 
1. Välj den **gäller dela** och välj **behållarnamn**.

![dimension][dimension]

## <a name="get-metrics---azure-cli"></a>Hämta mått – Azure CLI

Mått för container instances kan också samlas in med Azure CLI. Hämta först containergruppens ID med följande kommando. Ersätt `<resource-group>` med resursgruppens namn och `<container-group>` med namnet på containergruppen.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Använd följande kommando för att hämta **CPU**-användningsstatistik.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Ändra värdet för den `--metric` parameter i kommandot för att hämta andra [stöds mått][supported-metrics]. Till exempel använda följande kommando för att hämta **minne** användningsstatistik. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

För en grupp med flera behållare i `containerName` dimensionen kan läggas till att returnera mått per behållare.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-övervakning i [översikten över Azure övervakning][azure-monitoring].

Lär dig hur du skapar [måttaviseringar] [ metric-alert] att få ett meddelande när ett mått för Azure Container Instances överskrider ett tröskelvärde.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
