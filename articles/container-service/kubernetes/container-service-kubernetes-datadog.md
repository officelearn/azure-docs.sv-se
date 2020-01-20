---
title: FÖRÅLDRAD Övervaka Azure Kubernetes-kluster med Datadog
description: Övervaka Kubernetes-kluster i Azure Container Service med Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c8ed146a224ec4225a7a0e85c76227fb1dc71b0b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271056"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>FÖRÅLDRAD Övervaka ett Azure Container Service kluster med DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har `az` Azure CLI och `kubectl` verktyg installerat.

Du kan testa om du har installerat `az`-verktyget genom att köra:

```console
$ az --version
```

Om du inte har installerat `az`-verktyget finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har installerat `kubectl`-verktyget genom att köra:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat kan du köra:

```console
$ az acs kubernetes install-cli
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
