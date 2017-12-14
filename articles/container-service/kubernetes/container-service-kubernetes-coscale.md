---
title: "Övervaka ett Azure Kubernetes kluster med CoScale"
description: "Övervaka ett Kubernetes kluster i Azure Container Service med hjälp av CoScale"
services: container-service
author: fryckbos
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 2d6757397d76b6ca87a45254cb31f34d34a42541
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Övervaka ett Azure Container Service Kubernetes kluster med CoScale

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

I den här artikeln vi hur du distribuerar den [CoScale](https://www.coscale.com/) agenten ska övervaka alla noder och behållare i Kubernetes klustret i Azure Container Service. Du behöver ett konto med CoScale för den här konfigurationen. 


## <a name="about-coscale"></a>Om CoScale 

CoScale är en övervakning plattform som samlar in mätvärden och -händelser från alla behållare i flera orchestration-plattformar. CoScale ger fullständig stack övervakning för Kubernetes miljöer. Den innehåller visualiseringar och analytics för alla lager i stacken: OS, Kubernetes, Docker och program som körs på din behållare. CoScale erbjuder flera inbyggda övervakning instrumentpaneler och har inbyggda avvikelseidentifiering att operatörer och utvecklare att snabbt hitta problem infrastruktur och program.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

Som det visas i den här artikeln, kan du installera agenter på ett Kubernetes kluster som kör CoScale som ett SaaS-lösningen. Om du vill behålla dina data på plats är CoScale också tillgängligt för lokal installation.


## <a name="prerequisites"></a>Krav

Du måste först [skapa ett konto för CoScale](https://www.coscale.com/free-trial).

Den här genomgången förutsätter att du har [skapas ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätts även att du har den `az` Azure CLI och `kubectl` verktygen som installeras.

Du kan testa om du har den `az` installerat genom att köra verktyget:

```azurecli
az --version
```

Om du inte har den `az` verktyget är installerat, det finns instruktioner [här](/cli/azure/install-azure-cli).

Du kan testa om du har den `kubectl` installerat genom att köra verktyget:

```bash
kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installera agenten CoScale med en DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter, till exempel CoScale agenten.

När du loggar in på CoScale går du till den [agent sidan](https://app.coscale.com/) installera CoScale agenter på klustret med hjälp av en DaemonSet. CoScale Användargränssnittet innehåller interaktiv konfigurationssteg för att skapa en agent och börja övervaka fullständig Kubernetes klustret.

![CoScale agentkonfiguration](./media/container-service-kubernetes-coscale/installation.png)

Kör det angivna kommandot för att starta agenten på klustret:

![Starta CoScale-agent](./media/container-service-kubernetes-coscale/agent_script.png)

Klart! När agenterna är igång kan bör du se data i konsolen på några minuter. Besök den [agent sidan](https://app.coscale.com/) om du vill visa en sammanfattning av klustret, utföra ytterligare konfigurationssteg och se instrumentpaneler som den **Kubernetes kluster översikt**.

![Översikt över kluster Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale-agenten distribueras automatiskt på nya datorer i klustret. Agentuppdateringar automatiskt när en ny version släpps.


## <a name="next-steps"></a>Nästa steg

Finns det [CoScale dokumentationen](http://docs.coscale.com/) och [blogg](https://www.coscale.com/blog) för mer information om CoScale övervakningslösningar. 

