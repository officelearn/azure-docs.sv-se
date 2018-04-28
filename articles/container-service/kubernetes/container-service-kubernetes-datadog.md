---
title: Övervaka Azure Kubernetes kluster med Datadog
description: Övervaka Kubernetes kluster i Azure Container Service med hjälp av Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0a3f0baa4998dbc594023935575d659f7d45bbb9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Övervaka ett Azure Container Service-kluster med DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapas ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätts även att du har den `az` Azure cli och `kubectl` verktygen som installeras.

Du kan testa om du har den `az` installerat genom att köra verktyget:

```console
$ az --version
```

Om du inte har den `az` verktyget är installerat, det finns instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har den `kubectl` installerat genom att köra verktyget:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog är en övervakningstjänsten som samlar in övervakningsdata från din behållare i Azure Container Service-kluster. Datadog har en Docker-integrering instrumentpanel där du kan se specifika mått i en behållare. Mätvärden som samlats in från behållarna ordnas efter CPU, minne, nätverk och i/o. Datadog delar mått i behållare och bilder.

Du måste först [skapa ett konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installera agenten Datadog med en DaemonSet
DaemonSets som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter.

När du har loggat in på Datadog, kan du följa den [Datadog instruktioner](https://app.datadoghq.com/account/settings#agent/kubernetes) installera Datadog agenter på klustret med hjälp av en DaemonSet.

## <a name="conclusion"></a>Sammanfattning
Klart! Du bör se data i konsolen för några minuter när agenterna är igång. Du kan besöka den integrerade [kubernetes instrumentpanelen](https://app.datadoghq.com/screen/integration/kubernetes) visas en sammanfattning av klustret.
