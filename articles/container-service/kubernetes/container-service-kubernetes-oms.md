---
title: FÖRÅLDRAD Övervaka Azure Kubernetes-kluster – drift hantering
description: Övervaka Kubernetes-kluster i Azure Container Service att använda Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb500d2f00d6657420d7f294a7318b339e1f81e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271076"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>FÖRÅLDRAD Övervaka ett Azure Container Service kluster med Log Analytics

> [!TIP]
> Den uppdaterade versionen av den här artikeln som använder Azure Kubernetes-tjänsten finns i [Azure Monitor for containers](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätter också att du har `az` Azure CLI och `kubectl` verktyg installerat.

Du kan testa om du har installerat `az`-verktyget genom att köra:

```console
$ az --version
```

Om du inte har installerat `az`-verktyget finns det instruktioner [här](https://github.com/azure/azure-cli#installation).
Du kan också använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), som har `az` Azure cli och `kubectl` verktyg som redan har installerats åt dig.

Du kan testa om du har installerat `kubectl`-verktyget genom att köra:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat kan du köra:
```console
$ az acs kubernetes install-cli
```

För att testa om du har Kubernetes-nycklar som är installerade i kubectl-verktyget kan du köra:
```console
$ kubectl get nodes
```

Om ovanstående kommando fel uppstår måste du installera Kubernetes-kluster nycklar i kubectl-verktyget. Du kan göra det med följande kommando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Övervaka behållare med Log Analytics

Log Analytics är Microsofts molnbaserade IT-hanterings lösning som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur. Container lösning är en lösning i Log Analytics, som hjälper dig att Visa behållar inventering, prestanda och loggar på en enda plats. Du kan granska, felsöka behållare genom att visa loggarna på den centrala platsen och hitta störningar som konsumerar överskotts behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om container Solution finns i [behållar lösningen Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installera Log Analytics på Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Hämta ditt arbetsyte-ID och nyckel
För att Log Analytics agenten ska kunna kommunicera med tjänsten måste den konfigureras med ett arbetsyte-ID och en nyckel för arbets ytan. För att hämta arbetsyte-ID och nyckel måste du skapa ett konto på <https://mms.microsoft.com>.
Följ stegen för att skapa ett konto. När du har skapat kontot kan du hämta ditt ID och din nyckel genom att klicka på bladet **Log Analytics** och sedan på arbets ytans namn. Under **Avancerade inställningar**, **anslutna källor**och sedan **Linux-servrar**hittar du den information du behöver, som du ser nedan.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installera Log Analytics agent med hjälp av en DaemonSet
DaemonSets används av Kubernetes för att köra en enda instans av en behållare på varje värd i klustret.
De är perfekta för att köra övervaknings agenter.

Här är [filen DAEMONSET yaml](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Spara den till en fil med namnet `oms-daemonset.yaml` och ersätt plats hållarens värden för `WSID` och `KEY` med ditt arbetsyte-ID och nyckel i filen.

När du har lagt till din arbetsyte-ID och nyckel för DaemonSet-konfigurationen kan du installera Log Analytics agenten på klustret med kommando rads verktyget `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installera Log Analytics-agenten med en Kubernetes-hemlighet
För att skydda din Log Analytics arbetsyte-ID och nyckel kan du använda Kubernetes-hemlighet som en del av DaemonSet YAML-filen.

- Kopiera skriptet, den hemliga mallfilen och DaemonSet YAML-filen (från [databasen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) och kontrol lera att de finns i samma katalog.
  - skript för hemligt skapande – secret-gen.sh
  - Hemlig mall - hemlighet template.yaml
    - DaemonSet YAML File-omsagent-DS-hemligheter. yaml
- Kör skriptet. Skriptet kommer att fråga efter Log Analytics arbetsyte-ID och primär nyckel. Infoga det och skriptet skapar en hemlig yaml-fil så att du kan köra den.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Skapa hemligheterna Pod genom att köra följande: ```kubectl create -f omsagentsecret.yaml```

  - Du kan kontrollera genom att köra följande:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
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

  - Skapa din omsagent daemon-set genom att köra ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Slutsats
Klart! Efter några minuter bör du kunna se data som flödar till din Log Analytics-instrumentpanel.
