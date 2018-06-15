---
title: Övervaka Azure Kubernetes kluster - Verksamhetsstyrning
description: Övervaka Kubernetes kluster i Azure Container Service med hjälp av logganalys
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3b014ce4c91d1dc9fae744ef4b528c98f9f787b3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164327"
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Övervaka ett Azure Container Service-kluster med logganalys

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapas ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).

Det förutsätts även att du har den `az` Azure cli och `kubectl` verktygen som installeras.

Du kan testa om du har den `az` installerat genom att köra verktyget:

```console
$ az --version
```

Om du inte har den `az` verktyget är installerat, det finns instruktioner [här](https://github.com/azure/azure-cli#installation).
Du kan också använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), som har den `az` Azure cli och `kubectl` verktyg som redan har installerat för du.

Du kan testa om du har den `kubectl` installerat genom att köra verktyget:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:
```console
$ az acs kubernetes install-cli
```

Kontrollera att du har kubernetes nycklar som installerats i din kubectl verktyg som du kan köra:
```console
$ kubectl get nodes
```

Om ovanstående kommando felen ut, måste du installera kubernetes klustret nycklar i kubectl-verktyget. Du kan göra det med följande kommando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Övervakning av behållare med logganalys

Logganalys är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Behållaren lösningen är en lösning i logganalys som hjälper dig att visa behållaren inventering, prestanda och loggar på en enda plats. Du kan granska, Felsök behållare genom att visa loggarna på central plats och hitta störningar förbrukar överdriven behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om behållaren lösning hittar du den [behållare lösning logganalys](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installera logganalys på Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Hämta ditt arbetsyte-ID och nyckel
Agenten tala med tjänsten den måste konfigureras med en arbetsyte-ID och en arbetsyta för Log Analytics. Få arbetsyte-ID och nyckel måste du skapa ett konto på <https://mms.microsoft.com>.
Följ stegen för att skapa ett konto. När du är klar att skapa kontot, måste du skaffa din ID och nyckel genom att klicka på **inställningar**, sedan **anslutna källor**, och sedan **Linux-servrar**, enligt nedan.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installera agenten Log Analytics med hjälp av en DaemonSet
DaemonSets som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter.

Här är den [DaemonSet YAML filen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Spara den på en fil med namnet `oms-daemonset.yaml` och ersätter värdena platshållare för `WSID` och `KEY` med ditt arbetsyte-ID och nyckel i filen.

När du har lagt till ditt arbetsyte-ID och nyckel DaemonSet konfigurationen kan du kan installera logganalys-agenten på klustret med det `kubectl` kommandoradsverktyget:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installerar agenten Log Analytics med hjälp av en Kubernetes hemlighet
Du kan använda Kubernetes hemlighet som en del av DaemonSet YAML-filen för att skydda ditt logganalys arbetsyte-ID och nyckel.

 - Kopiera skriptet och hemliga mallfilen DaemonSet YAML-filen (från [databasen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) och kontrollera att de finns i samma katalog.
      - Hemligt genererar skript - hemlighet gen.sh
      - Hemlig mall - hemlighet template.yaml
   - Filen DaemonSet YAML - omsagent-ds-secrets.yaml
 - Kör skriptet. Skriptet begär Log Analytics arbetsyte-ID och primärnyckel. Infoga som och skriptet skapar en hemlig yaml-fil så att du kan köra den.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Skapa hemligheter baljor genom att köra följande: ``` kubectl create -f omsagentsecret.yaml ```

   - Om du vill kontrollera, kör du följande:

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

  - Skapa din omsagent daemon set genom att köra ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Sammanfattning
Klart! Du ska kunna se data som flödar till instrumentpanelen logganalys efter några minuter.
