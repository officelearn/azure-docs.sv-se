---
title: (FÖRÅLDRAD) Övervaka Azure Kubernetes-kluster - Sysdig
description: Övervaka Kubernetes-kluster i Azure Container Service med Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371108"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(FÖRÅLDRAD) Övervaka ett Azure Container Service Kubernetes-kluster med Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
Den här genomgången förutsätter att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har azure cli och kubectl verktyg installerade.

Du kan testa om `az` du har verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har `az` verktyget installerat finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om `kubectl` du har verktyget installerat genom att köra:

```console
kubectl version
```

Om du inte `kubectl` har installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig är en extern övervakning som ett serviceföretag som kan övervaka behållare i kubernetes-klustret som körs i Azure. Att använda Sysdig kräver ett aktivt Sysdig-konto.
Du kan registrera dig för ett konto på deras [webbplats](https://app.sysdigcloud.com).

När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Installera Sysdig-agenterna i Kubernetes
För att övervaka dina behållare kör Sysdig en process `DaemonSet`på varje dator med hjälp av en Kubernetes .
DemonSet är Kubernetes API-objekt som kör en enda instans av en behållare per dator.
De är perfekta för att installera verktyg som Sysdigs övervakningsagent.

Om du vill installera Sysdig-demonuppsättningen bör du först hämta [mallen](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) från sysdig. Spara filen `sysdig-daemonset.yaml`som .

På Linux och OS X kan du köra:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

I PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Nästa redigera filen för att infoga din Åtkomstnyckel, som du fått från ditt Sysdig-konto.

Slutligen, skapa DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Visa din övervakning
När agenter har installerats och körts ska de pumpa tillbaka data till Sysdig.  Gå tillbaka till [instrumentpanelen sysdig](https://app.sysdigcloud.com) och du bör se information om dina behållare.

Du kan också installera Kubernetes-specifika instrumentpaneler via den [nya instrumentpanelsguiden](https://app.sysdigcloud.com/#/dashboards/new).
