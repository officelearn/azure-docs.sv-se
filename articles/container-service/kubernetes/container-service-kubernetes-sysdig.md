---
title: "Övervaka Azure Kubernetes kluster - Sysdig"
description: "Övervaka Kubernetes kluster i Azure Container Service med hjälp av Sysdig"
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4ff610f72af4e6a750749009f3cd4b4df632a37f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Övervaka en Kubernetes för Azure Container Service-kluster med hjälp av Sysdig

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Krav
Den här genomgången förutsätter att du har [skapas ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätts även att du har azure cli och kubectl verktygen som installeras.

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

## <a name="sysdig"></a>Sysdig
Sysdig är en extern övervakning som en tjänst-företag som du kan övervaka behållare i Kubernetes klustret körs i Azure. Med hjälp av Sysdig kräver ett aktivt Sysdig-konto.
Du kan registrera dig för ett konto sina [plats](https://app.sysdigcloud.com).

När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Installera Sysdig-agenter till Kubernetes
Om du vill övervaka behållarna Sysdig kör en process på varje dator med en Kubernetes `DaemonSet`.
DaemonSets är Kubernetes API-objekt som kör en instans av en behållare per dator.
Det är perfekt för att installera verktyg som den Sysdig övervakningsagenten.

Om du vill installera Sysdig daemonset, bör du först hämta [mallen](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) från sysdig. Spara filen som `sysdig-daemonset.yaml`.

Du kan köra på Linux och OS X:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

I PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Nästa redigera filen för att infoga snabbtangent, som du fick från ditt konto för Sysdig.

Skapa slutligen DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Visa övervakningen
När du installerade och körs, bör agenterna pump data tillbaka till Sysdig.  Gå tillbaka till den [sysdig instrumentpanelen](https://app.sysdigcloud.com) och du bör se information om din behållare.

Du kan också installera Kubernetes-specifika instrumentpaneler via den [guiden Ny instrumentpanel](https://app.sysdigcloud.com/#/dashboards/new).
