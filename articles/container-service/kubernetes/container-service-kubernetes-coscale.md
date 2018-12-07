---
title: (INAKTUELL) Övervaka ett Azure Kubernetes-kluster med CoScale
description: Övervaka ett Kubernetes-kluster i Azure Container Service med CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 895346447e33926dcaa5ca09302f35c9d6636ed9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000237"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(INAKTUELL) Övervaka ett Azure Container Service Kubernetes-kluster med CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här artikeln visar vi dig hur du distribuerar den [CoScale](https://web.archive.org/web/20180317071550/ https://www.coscale.com/) agent för att övervaka alla noderna och behållarna i ett Kubernetes-kluster i Azure Container Service. Du behöver ett konto med CoScale för den här konfigurationen. 


## <a name="about-coscale"></a>Om CoScale 

CoScale är en övervakningsplattform som samlar in mått och händelser från alla behållare i flera orchestration-plattformar. CoScale erbjuder fullständig övervakning för Kubernetes-miljöer. Det ger visualiseringar och analysverktyg för alla lager i stacken: OS, Kubernetes, Docker och program som körs i dina behållare. CoScale erbjuder flera inbyggda övervakning instrumentpaneler och har inbyggd avvikelseidentifiering att tillåta operatorer och utvecklare att snabbt hitta problem med infrastruktur och program.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

I den här artikeln visas kan du installera agenter på ett Kubernetes-kluster för att köra CoScale som en SaaS-lösning. Om du vill behålla dina data på plats är CoScale också tillgängliga för en lokal installation.


## <a name="prerequisites"></a>Förutsättningar

Du måste först [skapa ett konto med CoScale](https://web.archive.org/web/20170507123133/ https://www.coscale.com/free-trial).

Den här genomgången förutsätter att du har [skapade ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Den förutsätter också att du har den `az` Azure CLI och `kubectl` tools har installerats.

Du kan testa om du har den `az` verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har den `az` verktyget installerat, det finns anvisningar [här](/cli/azure/install-azure-cli).

Du kan testa om du har den `kubectl` verktyget installerat genom att köra:

```bash
kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installera agenten CoScale med ett DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter, till exempel CoScale agenten.

När du loggar in på CoScale går du till den [agent-sidan](https://app.coscale.com/) att installera CoScale agenter i klustret med hjälp av ett DaemonSet. Gränssnittet för CoScale ger guidad konfigurationssteg för att skapa en agent och börja övervaka fullständig Kubernetes-klustret.

![CoScale agentkonfiguration](./media/container-service-kubernetes-coscale/installation.png)

Om du vill starta agenten på klustret, kör du det angivna kommandot:

![Starta agenten CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Klart! När agenterna är igång kan bör du se data i konsolen på några få minuter. Gå till den [agent-sidan](https://app.coscale.com/) för att visa en sammanfattning av ditt kluster, utföra ytterligare konfigurationssteg och se instrumentpaneler som den **Kubernetes-kluster översikt**.

![Översikt över Kubernetes-kluster](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale-agenten distribueras automatiskt på nya datorer i klustret. Agentuppdateringar automatiskt när en ny version har släppts.


## <a name="next-steps"></a>Nästa steg

Se den [CoScale dokumentation](https://web.archive.org/web/20180415164304/ http://docs.coscale.com:80/) och [blogg](https://web.archive.org/web/20170501021344/ http://www.coscale.com:80/blog) för mer information om CoScale övervakningslösningar. 

