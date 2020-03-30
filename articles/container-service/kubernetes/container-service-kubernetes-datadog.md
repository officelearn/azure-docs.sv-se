---
title: (FÖRÅLDRAD) Övervaka Azure Kubernetes-kluster med Datadog
description: Övervaka Kubernetes-kluster i Azure Container Service med Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371179"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(FÖRÅLDRAD) Övervaka ett Azure Container Service-kluster med DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
Den här genomgången förutsätter att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att `az` du `kubectl` har Azure cli och verktyg installerade.

Du kan testa om `az` du har verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har `az` verktyget installerat finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om `kubectl` du har verktyget installerat genom att köra:

```console
kubectl version
```

Om du inte `kubectl` har installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog (DataDog)
Datadog är en övervakningstjänst som samlar in övervakningsdata från dina behållare i azure container service-klustret. Datadog har en Docker Integration Dashboard där du kan se specifika mått i dina behållare. Mått som samlas in från dina behållare ordnas efter CPU, Minne, Nätverk och I/O. Datadog delar upp mätvärden i behållare och bilder.

Du måste först [skapa ett konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installera Datadog Agent med en DemonSet
DaemonSets används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att sköta övervakningsagenter.

När du har loggat in på Datadog kan du följa [Datadogs instruktioner](https://app.datadoghq.com/account/settings#agent/kubernetes) för att installera Datadog-agenter i klustret med hjälp av en DaemonSet.

## <a name="conclusion"></a>Slutsats
Klart! När agenterna är igång bör du se data i konsolen om några minuter. Du kan besöka [instrumentpanelen för integrerade kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) för att se en sammanfattning av klustret.
