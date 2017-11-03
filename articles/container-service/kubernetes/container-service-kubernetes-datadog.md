---
title: "Övervaka Azure Kubernetes kluster med Datadog | Microsoft Docs"
description: "Övervaka Kubernetes kluster i Azure Container Service med hjälp av Datadog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: b2f88f663745f1caef856e9b2b5803000c284cb9
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Övervaka ett Azure Container Service-kluster med DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Krav
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

## <a name="conclusion"></a>Slutsats
Klart! Du bör se data i konsolen för några minuter när agenterna är igång. Du kan besöka den integrerade [kubernetes instrumentpanelen](https://app.datadoghq.com/screen/integration/kubernetes) visas en sammanfattning av klustret.
