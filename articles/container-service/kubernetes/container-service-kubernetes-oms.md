---
title: Övervaka Azure Kubernetes-kluster – åtgärdshantering
description: Övervaka Kubernetes-kluster i Azure Container Service med hjälp av Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: a353fe3803b2d93c151559076960df06eb260bfe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426421"
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Övervaka ett Azure Container Service-kluster med Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapade ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Den förutsätter också att du har den `az` Azure cli och `kubectl` tools har installerats.

Du kan testa om du har den `az` verktyget installerat genom att köra:

```console
$ az --version
```

Om du inte har den `az` verktyget installerat, det finns anvisningar [här](https://github.com/azure/azure-cli#installation).
Du kan också använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), som har den `az` Azure cli och `kubectl` verktyg som installerats för dig.

Du kan testa om du har den `kubectl` verktyget installerat genom att köra:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:
```console
$ az acs kubernetes install-cli
```

Kontrollera att du har kubernetes-nycklar som installerats i din kubectl-verktyg som du kan köra:
```console
$ kubectl get nodes
```

Om ovanstående kommando felen ut, måste du installera kubernetes-kluster nycklar i ditt kubectl-verktyg. Du kan göra det med följande kommando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Behållarövervakning med Log Analytics

Log Analytics är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Behållarlösningen är en lösning i Log Analytics, som hjälper dig att visa behållaren inventering, prestanda och loggar på en enda plats. Du kan granska, Felsök behållare genom att visa loggarna på central plats och ta bort störande förbrukar överflödiga behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om Behållarlösningen finns i den [behållare lösning Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installera Log Analytics på Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Hämta ditt arbetsyte-ID och nyckel
Agenten att kommunicera med tjänsten den måste konfigureras med en arbetsyte-ID och arbetsytenyckel för en för Log Analytics. Hämta arbetsytans ID och nyckel måste du skapa ett konto på <https://mms.microsoft.com>.
Följ stegen för att skapa ett konto. När du är klar att skapa konto, du kan hämta ditt ID och nyckel genom att klicka på den **Log Analytics** bladet och sedan namnet på din arbetsyta. Sedan, under **avancerade inställningar**, **anslutna källor**, och sedan **Linux-servrar**, hittar du den information du behöver enligt nedan.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installera Log Analytics-agenten med hjälp av ett DaemonSet
DaemonSets som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter.

Här är den [DaemonSet YAML-fil](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Spara den till en fil med namnet `oms-daemonset.yaml` och ersätter värdena för platshållare för `WSID` och `KEY` med arbetsyte-ID och nyckel i filen.

När du har lagt till ditt arbetsyte-ID och nyckel DaemonSet konfigurationen, du kan installera Log Analytics-agenten på klustret med den `kubectl` kommandoradsverktyget:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installera Log Analytics-agenten med hjälp av en Kubernetes-hemlighet
Du kan använda Kubernetes-hemlighet som en del av DaemonSet YAML-fil för att skydda ditt Log Analytics arbetsyte-ID och nyckel.

 - Kopiera skriptet och hemliga mallfilen DaemonSet YAML-fil (från [databasen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) och kontrollera att de finns i samma katalog.
      - Hemligheten som genererar skript - hemlighet gen.sh
      - Hemlig mall - hemlighet template.yaml
   - DaemonSet YAML-fil – omsagent-ds-secrets.yaml
 - Kör skriptet. Skriptet begär Log Analytics arbetsyte-ID och den primärnyckeln. Infoga som och skriptet skapar en hemlig yaml-fil så att du kan köra den.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Skapa hemligheter pod genom att köra följande: ``` kubectl create -f omsagentsecret.yaml ```

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

  - Skapa din omsagent daemon-set genom att köra ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Sammanfattning
Klart! Efter några minuter bör du kunna se data flöda till instrumentpanelen i Log Analytics.
