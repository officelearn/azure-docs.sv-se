---
title: "Övervaka Azure Kubernetes kluster - Verksamhetsstyrning"
description: "Övervaka Kubernetes kluster i Azure Container Service med hjälp av Microsoft Operations Management Suite"
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: e8a68896c923d785fea84cef60f8d2015906f342
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Övervaka ett Azure Container Service-kluster med Microsoft Operations Management Suite (OMS)

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Krav
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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Övervakning av behållare med Operations Management Suite (OMS)

Microsoft Operations Management (OMS) är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Behållaren lösningen är en lösning i OMS logganalys, vilket gör det möjligt att visa behållaren inventering, prestanda och loggar på en enda plats. Du kan granska, Felsök behållare genom att visa loggarna på central plats och hitta störningar förbrukar överdriven behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om behållaren lösning hittar du den [behållare lösning logganalys](../../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Installera OMS på Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Hämta ditt arbetsyte-ID och nyckel
Agenten tala med tjänsten den måste konfigureras med en arbetsyte-id och en arbetsyta för OMS. Få arbetsyte-id och nyckel måste du skapa en OMS-konto på <https://mms.microsoft.com>. Följ stegen för att skapa ett konto. När du är klar att skapa kontot, måste du skaffa din id och nyckel genom att klicka på **inställningar**, sedan **anslutna källor**, och sedan **Linux-servrar**, enligt nedan.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Installera OMS-agenten med hjälp av en DaemonSet
DaemonSets som används av Kubernetes för att köra en instans av en behållare på varje värd i klustret.
Det är perfekt för att köra övervakningsagenter.

Här är den [DaemonSet YAML filen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Spara den på en fil med namnet `oms-daemonset.yaml` och ersätter värdena platshållare för `WSID` och `KEY` med ditt arbetsyte-id och nyckel i filen.

När du har lagt till ditt arbetsyte-ID och nyckel DaemonSet konfigurationen kan du kan installera OMS-agenten på klustret med det `kubectl` kommandoradsverktyget:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Installerar OMS-agenten med hjälp av en Kubernetes hemlighet
För att skydda din OMS arbetsyte-ID och du kan använda Kubernetes hemlighet som en del av DaemonSet YAML-fil.

 - Kopiera skriptet, hemliga mallfilen och DaemonSet YAML-filen (från [databasen](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) och kontrollera att de finns i samma katalog. 
      - Hemligt genererar skript - hemlighet gen.sh
      - Hemlig mall - hemlighet template.yaml
   - Filen DaemonSet YAML - omsagent-ds-secrets.yaml
 - Kör skriptet. Skriptet begär OMS arbetsyte-ID och primärnyckel. In sätt som och skriptet skapar en hemlig yaml-fil så att du kan köra den.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Skapa hemligheter baljor genom att köra följande:``` kubectl create -f omsagentsecret.yaml ```
 
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
 
  - Skapa din omsagent daemon set genom att köra``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Slutsats
Klart! Efter några minuter, bör du kunna se data som flödar till OMS-instrumentpanelen.
