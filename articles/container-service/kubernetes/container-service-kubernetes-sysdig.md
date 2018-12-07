---
title: (INAKTUELL) Övervaka Azure Kubernetes-kluster – Sysdig
description: Övervaka Kubernetes-kluster i Azure Container Service med Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998735"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(INAKTUELL) Övervaka ett Azure Container Service Kubernetes-kluster med Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapade ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Den förutsätter också att du har azure cli och kubectl verktygen som installeras.

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

## <a name="sysdig"></a>Sysdig
Sysdig är en extern övervakning som en tjänst-företag som kan övervaka behållare i ett Kubernetes-kluster som körs i Azure. Med hjälp av Sysdig kräver ett aktivt Sysdig-konto.
Du kan registrera dig för ett konto sina [plats](https://app.sysdigcloud.com).

När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Installera Sysdig-agenter till Kubernetes
Om du vill övervaka din behållare, Sysdig kör en process på varje dator som använder en Kubernetes `DaemonSet`.
DaemonSets är Kubernetes API-objekt som kör en instans av en behållare per dator.
Det är perfekt för att installera verktyg som de Sysdig övervakningsagent.

Om du vill installera Sysdig-daemonset, bör du först hämta [mallen](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) från sysdig. Spara filen som `sysdig-daemonset.yaml`.

På Linux och OS X kan du köra:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

I PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Redigera sedan filen om du vill infoga din åtkomstnyckel som du fick från ditt Sysdig-konto.

Skapa slutligen ett DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Se övervakningen
När du installerade och körs, bör agenterna pump data tillbaka till Sysdig.  Gå tillbaka till den [sysdig instrumentpanelen](https://app.sysdigcloud.com) så bör du se information om din behållare.

Du kan också installera Kubernetes-specifika instrumentpaneler via den [nya instrumentpanelsguiden](https://app.sysdigcloud.com/#/dashboards/new).
