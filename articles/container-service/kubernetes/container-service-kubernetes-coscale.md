---
title: FÖRÅLDRAD Övervaka ett Azure Kubernetes-kluster med samskala
description: Övervaka ett Kubernetes-kluster i Azure Container Service med hjälp av samskala
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681719"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>FÖRÅLDRAD Övervaka ett Azure Container Service Kubernetes-kluster med samskala

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här artikeln visar vi hur du distribuerar en [storskalig](https://web.archive.org/web/20180317071550/https://www.coscale.com/) agent för att övervaka alla noder och behållare i ditt Kubernetes-kluster i Azure Container Service. Du behöver ett konto med samskala för den här konfigurationen. 


## <a name="about-coscale"></a>Om samskala 

Samskala är en övervaknings plattform som samlar in mått och händelser från alla behållare på flera Dirigerings plattformar. Med Samskalan får du full stack-övervakning för Kubernetes-miljöer. Den innehåller visualiseringar och analyser för alla skikt i stacken: operativ systemet, Kubernetes, Docker och program som körs i dina behållare. Samskala erbjuder flera inbyggda övervaknings instrument paneler och har inbyggd avvikelse identifiering för att tillåta operatörer och utvecklare att snabbt hitta infrastruktur-och program problem.

![Användar gränssnitt för samskala](./media/container-service-kubernetes-coscale/coscale.png)

Som du ser i den här artikeln kan du installera agenter i ett Kubernetes-kluster för att köra samskala som en SaaS-lösning. Om du vill behålla data på plats är samskala också tillgänglig för lokal installation.


## <a name="prerequisites"></a>Krav

Du måste först [skapa ett konto för samskala](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har `az` installerat Azure CLI `kubectl` och verktyg.

Du kan testa om du har installerat `az` verktyget genom att köra:

```azurecli
az --version
```

Om du inte har installerat `az` verktyget finns det instruktioner [här](/cli/azure/install-azure-cli).

Du kan testa om du har installerat `kubectl` verktyget genom att köra:

```bash
kubectl version
```

Om du inte har `kubectl` installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installera en samskalnings agent med en DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att köra övervaknings agenter som till exempel en storskalig agent.

När du har loggat in på Samskalan går du till [agent sidan](https://developer.newrelic.com/) för att installera samskalande agenter i klustret med hjälp av en DaemonSet. Samskalnings gränssnittet innehåller guidade konfigurations steg för att skapa en agent och börja övervaka ditt kompletta Kubernetes-kluster.

![Konfiguration av samskalad agent](./media/container-service-kubernetes-coscale/installation.png)

Starta agenten på klustret genom att köra det angivna kommandot:

![Starta den storskaliga agenten](./media/container-service-kubernetes-coscale/agent_script.png)

Klart! När agenterna är igång bör du se data i-konsolen på några få minuter. Besök [sidan agent](https://developer.newrelic.com/) om du vill se en sammanfattning av klustret, utföra ytterligare konfigurations steg och se instrument paneler som Kubernetes- **klustrets översikt**.

![Översikt över Kubernetes-kluster](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Superskalnings agenten distribueras automatiskt på nya datorer i klustret. Agenten uppdateras automatiskt när en ny version släpps.


## <a name="next-steps"></a>Nästa steg

Se [dokumentationen](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) och [bloggen](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) för att få mer information om storskaliga övervaknings lösningar. 

