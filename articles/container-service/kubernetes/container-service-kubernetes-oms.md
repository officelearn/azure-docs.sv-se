---
title: (FÖRÅLDRAD) Övervaka Azure Kubernetes-kluster – Operations Management
description: Övervaka Kubernetes-kluster i Azure Container Service med hjälp av Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371162"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(FÖRÅLDRAD) Övervaka ett Azure Container Service-kluster med Log Analytics

> [!TIP]
> Den uppdaterade versionen som används i den här artikeln som använder Azure Kubernetes-tjänsten finns i [Azure Monitor för behållare](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
Den här genomgången förutsätter att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att `az` du `kubectl` har Azure cli och verktyg installerade.

Du kan testa om `az` du har verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har `az` verktyget installerat finns det instruktioner [här](https://github.com/azure/azure-cli#installation).
Alternativt kan du använda [Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview)Shell `az` , `kubectl` som har Azure cli och verktyg som redan är installerade för dig.

Du kan testa om `kubectl` du har verktyget installerat genom att köra:

```console
kubectl version
```

Om du inte `kubectl` har installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

Så här testar du om du har kubernetes-nycklar installerade i kubectl-verktyget kan du köra:

```console
kubectl get nodes
```

Om kommandot ovan fel felas måste du installera kubernetes-klusternycklar i kubectl-verktyget. Du kan göra det med följande kommando:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Övervaka behållare med logganalys

Log Analytics är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur.Container Solution är en lösning i Log Analytics, som hjälper dig att visa behållarinventet, prestanda och loggar på en enda plats. Du kan granska, felsöka behållare genom att visa loggarna på centraliserad plats och hitta bullriga förbrukar överskott behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om behållarlösning finns i [Container Solution Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installera Logganalys på Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Skaffa ditt arbetsyte-ID och -nyckel
För att Log Analytics-agenten ska kunna prata med tjänsten måste den konfigureras med ett arbetsområdes-ID och en arbetsytasnyckel. För att få arbetsytan ID och nyckel <https://mms.microsoft.com>måste du skapa ett konto på .
Följ stegen för att skapa ett konto. När du är klar med att skapa kontot kan du få ditt ID och din nyckel genom att klicka på **log analytics-bladet** och sedan namnet på din arbetsyta. Sedan, under **avancerade inställningar**, **anslutna källor**, och sedan **Linux-servrar**, hittar du den information du behöver, som visas nedan.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installera Log Analytics-agenten med hjälp av en DaemonSet
DaemonSets används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att sköta övervakningsagenter.

Här är [DaemonSet YAML fil](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Spara den i `oms-daemonset.yaml` en fil som heter `WSID` och `KEY` ersätt platshållarvärdena för och med arbetsyte-ID och nyckel i filen.

När du har lagt till ditt arbetsyte-ID och nyckeln till DaemonSet-konfigurationen kan du installera Log Analytics-agenten `kubectl` i klustret med kommandoradsverktyget:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installera Log Analytics-agenten med hjälp av en Kubernetes Secret
För att skydda ditt Log Analytics arbetsyte-ID och nyckel kan du använda Kubernetes Secret som en del av DaemonSet YAML-fil.

- Kopiera skriptet, den hemliga mallfilen och DaemonSet YAML-filen (från [databasen)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)och se till att de finns i samma katalog.
  - hemlig generera skript - secret-gen.sh
  - hemlig mall - secret-template.yaml
    - DaemonSet YAML fil - omsagent-ds-secrets.yaml
- Kör skriptet. Skriptet kommer att be om Log Analytics Workspace ID och primärnyckel. Infoga det och skriptet kommer att skapa en hemlig yaml-fil så att du kan köra den.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Skapa hemligheter pod genom att köra följande:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Kör följande för att kontrollera:

  ```console
  kubectl get secrets
  ```

  ```output
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Skapa din omsagent daemon-set genom att köra följande:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Slutsats
Klart! Efter några minuter bör du kunna se data flödar till instrumentpanelen i Log Analytics.
