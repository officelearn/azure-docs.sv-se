---
title: (FÖRÅLDRAD) Övervaka ett Azure Kubernetes-kluster med CoScale
description: Övervaka ett Kubernetes-kluster i Azure Container Service med CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271102"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(FÖRÅLDRAD) Övervaka ett Azure Container Service Kubernetes-kluster med CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här artikeln visar vi hur du distribuerar [CoScale-agenten](https://web.archive.org/web/20180317071550/https://www.coscale.com/) för att övervaka alla noder och behållare i kubernetes-klustret i Azure Container Service. Du behöver ett konto hos CoScale för den här konfigurationen. 


## <a name="about-coscale"></a>Om CoScale 

CoScale är en övervakningsplattform som samlar in mått och händelser från alla behållare i flera orkestreringsplattformar. CoScale erbjuder full-stack övervakning för Kubernetes miljöer. Det ger visualiseringar och analyser för alla lager i stapeln: OS, Kubernetes, Docker och program som körs inuti dina behållare. CoScale erbjuder flera inbyggda instrumentpaneler för övervakning, och det har inbyggd avvikelseidentifiering så att operatörer och utvecklare snabbt kan hitta infrastruktur- och programproblem.

![Gränssnitt för coscale-gränssnitt](./media/container-service-kubernetes-coscale/coscale.png)

Som visas i den här artikeln kan du installera agenter i ett Kubernetes-kluster för att köra CoScale som en SaaS-lösning. Om du vill behålla dina data på plats är CoScale också tillgängligt för lokal installation.


## <a name="prerequisites"></a>Krav

Du måste först [skapa ett CoScale-konto](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Den här genomgången förutsätter att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att `az` du `kubectl` har Azure CLI och verktyg installerade.

Du kan testa om `az` du har verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har `az` verktyget installerat finns det instruktioner [här](/cli/azure/install-azure-cli).

Du kan testa om `kubectl` du har verktyget installerat genom att köra:

```bash
kubectl version
```

Om du inte `kubectl` har installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installera CoScale-agenten med en DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att köra övervakningsagenter som CoScale-agenten.

När du har loggat in på CoScale går du till [agentsidan](https://app.coscale.com/) för att installera coscale-agenter i klustret med hjälp av en DaemonSet. CoScale-användargränssnittet innehåller guidade konfigurationssteg för att skapa en agent och börja övervaka ditt kompletta Kubernetes-kluster.

![Konfiguration av coscale-agent](./media/container-service-kubernetes-coscale/installation.png)

Om du vill starta agenten i klustret kör du det medföljande kommandot:

![Starta CoScale-agenten](./media/container-service-kubernetes-coscale/agent_script.png)

Klart! När agenterna är igång bör du se data i konsolen om några minuter. Besök [agentsidan](https://app.coscale.com/) för att se en sammanfattning av klustret, utföra ytterligare konfigurationssteg och se instrumentpaneler som **kubernetes-klusteröversikten**.

![Kubernetes klusteröversikt](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale-agenten distribueras automatiskt på nya datorer i klustret. Agenten uppdateras automatiskt när en ny version släpps.


## <a name="next-steps"></a>Nästa steg

Mer information om [coscale-övervakningslösningar](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) finns i dokumentationen och [bloggen](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) för coscale. 

