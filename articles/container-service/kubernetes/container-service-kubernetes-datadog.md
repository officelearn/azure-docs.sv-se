---
title: Övervaka Azure Kubernetes-kluster med Datadog
description: Övervaka Kubernetes-kluster i Azure Container Service med Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0a3f0baa4998dbc594023935575d659f7d45bbb9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38629077"
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Övervaka ett Azure Container Service-kluster med DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapade ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Den förutsätter också att du har den `az` Azure cli och `kubectl` tools har installerats.

Du kan testa om du har den `az` verktyget installerat genom att köra:

```console
$ az --version
```

Om du inte har den `az` verktyget installerat, det finns anvisningar [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har den `kubectl` verktyget installerat genom att köra:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog är en övervakningstjänst som samlar in övervakningsdata från dina behållare i Azure Container Service-klustret. Datadog har en instrumentpanel för integrering av Docker där du kan se specifika mått i dina behållare. Mätvärden som samlats in från dina behållare är ordnade efter processor, minne, nätverk och i/o. Datadog delar mått i behållare och bilder.

Du måste först [skapa ett konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installera agenten Datadog med ett DaemonSet
DaemonSets som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter.

När du är inloggad på Datadog, kan du följa den [Datadog instruktioner](https://app.datadoghq.com/account/settings#agent/kubernetes) att installera Datadog agenter i klustret med hjälp av ett DaemonSet.

## <a name="conclusion"></a>Sammanfattning
Klart! När agenterna är igång kan du bör se data i konsolen på några få minuter. Du kan gå till de integrerade [kubernetes-instrumentpanelen](https://app.datadoghq.com/screen/integration/kubernetes) att visas en sammanfattning av ditt kluster.
