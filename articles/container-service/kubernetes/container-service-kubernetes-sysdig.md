---
title: FÖRÅLDRAD Övervaka Azure Kubernetes-kluster – Sysdig
description: Övervaka Kubernetes-kluster i Azure Container Service med Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb9c628993201553b8da1d1bd37b4705e0f23dc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271641"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>FÖRÅLDRAD Övervaka ett Azure Container Service Kubernetes-kluster med Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har installerat Azure CLI-och kubectl-verktygen.

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

## <a name="sysdig"></a>Sysdig
Sysdig är en extern övervakning som ett tjänst företag som kan övervaka behållare i ditt Kubernetes-kluster som körs i Azure. Om du använder Sysdig krävs ett aktivt Sysdig-konto.
Du kan registrera dig för ett konto på sin [plats](https://app.sysdigcloud.com).

När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Installera Sysdig-agenterna på Kubernetes
För att övervaka dina behållare kör Sysdig en process på varje dator som använder en Kubernetes-`DaemonSet`.
DaemonSets är Kubernetes API-objekt som kör en enda instans av en behållare per dator.
De är perfekta för att installera verktyg som Sysdig Monitoring Agent.

Om du vill installera Sysdig-daemonset bör du först ladda ned [mallen](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) från Sysdig. Spara filen som `sysdig-daemonset.yaml`.

Du kan köra på Linux och OS X:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

I PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Redigera sedan filen för att infoga din åtkomst nyckel som du har fått från ditt Sysdig-konto.

Skapa slutligen DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Visa din övervakning
När agenterna har installerats och körts, ska de pumpa data tillbaka till Sysdig.  Gå tillbaka till [Sysdig-instrumentpanelen](https://app.sysdigcloud.com) och se information om dina behållare.

Du kan också installera Kubernetes-/regionsspecifika instrument paneler via [guiden Ny instrument panel](https://app.sysdigcloud.com/#/dashboards/new).
