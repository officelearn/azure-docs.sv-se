---
title: FÖRÅLDRAD Övervaka Azure Kubernetes-kluster med Datadog
description: Övervaka Kubernetes-kluster i Azure Container Service med Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371179"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>FÖRÅLDRAD Övervaka ett Azure Container Service kluster med DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har `az` installerat Azure CLI och `kubectl` verktyg.

Du kan testa om du har `az` installerat verktyget genom att köra:

```azurecli
az --version
```

Om du inte har `az` installerat verktyget finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har `kubectl` installerat verktyget genom att köra:

```console
kubectl version
```

Om du inte har `kubectl` installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog är en övervaknings tjänst som samlar in övervaknings data från dina behållare i ditt Azure Container Service-kluster. Datadog har en Docker-integrations instrument panel där du kan se vissa mått i dina behållare. Mått som samlas in från dina behållare ordnas efter processor, minne, nätverk och I/O. Datadog delar upp mått i behållare och avbildningar.

Du måste först [skapa ett konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installera Datadog-agenten med en DaemonSet
DaemonSets används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att köra övervaknings agenter.

När du har loggat in på Datadog kan du följa [Datadog-instruktionerna](https://app.datadoghq.com/account/settings#agent/kubernetes) för att installera Datadog-agenter i klustret med hjälp av en DaemonSet.

## <a name="conclusion"></a>Slutsats
Klart! När agenterna är igång bör du se data i-konsolen på några få minuter. Du kan gå till den integrerade [Kubernetes-instrumentpanelen](https://app.datadoghq.com/screen/integration/kubernetes) för att se en sammanfattning av klustret.
