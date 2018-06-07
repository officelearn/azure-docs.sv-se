---
title: Behållaren övervakning lösning i Azure Log Analytics | Microsoft Docs
description: Lösning för övervakning av behållare i logganalys hjälper dig se och hantera Docker- och Windows behållaren värdar i en enda plats.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 66d3ee76308573bb5672ffa8eeedcc4aca4c0cc5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638355"
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Lösning för övervakning av behållare i logganalys

![Behållare symbol](./media/log-analytics-containers/containers-symbol.png)

Den här artikeln beskriver hur du konfigurerar och använder lösning för övervakning av behållare i logganalys, vilket gör det möjligt att visa och hantera Docker- och Windows behållaren värdar i en enda plats. Docker är ett system för virtualisering av programvara som används för att skapa en behållare som automatiserar programdistribution till sina IT-infrastruktur.

Lösningen visar vilka behållare är kör, vilka behållare avbildningen de kör och där behållare körs. Du kan visa detaljerad granskningsinformation visar kommandon som används i behållare. Och du kan felsöka behållare genom att visa och söka centraliserad loggar utan att visa Docker- eller Windows-värdar via fjärranslutning. Du kan hitta behållare som kan vara mycket brus och mycket överflödiga resurser på en värd. Och du kan visa centraliserad CPU, minne, lagring och nätverksinformation om användning och prestanda för behållare. På datorer som kör Windows, kan du centralisera och jämför loggar från Windows Server Hyper-V och Docker-behållare. Lösningen stöder följande behållare orchestrators:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Om du vill övervaka prestanda för din arbetsbelastning distribueras till Kubernetes miljöer värdbaserade på AKS (Azure Container Service), se [övervakaren Azure Container Service](../monitoring/monitoring-container-health.md).  Övervakning av behållare lösningen inkluderar inte stöd för att övervaka den plattformen.  

Följande diagram visar relationerna mellan olika behållare värdar och -agenter med logganalys.

![Behållare diagram](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systemkrav och plattformar som stöds

Innan du börjar måste du granska följande information för att verifiera att du uppfyller kraven.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Behållaren övervakningslösning som stöd för Docker Orchestrator och OS-plattformen
I följande tabell beskrivs de Docker orchestration och stöd för behållaren inventering, prestanda och loggar med logganalys övervakning av operativsystemet.   

| | ACS | Linux | Windows | Behållare<br>Inventering | Bild<br>Inventering | Node<br>Inventering | Behållare<br>Prestanda | Behållare<br>Händelse | Händelse<br>Logga | Behållare<br>Logga |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Tjänst<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat öppna<br>Skift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(fristående) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>(fristående) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Docker-versioner som stöds på Linux

- Docker 1.11 1.13
- Docker CE och EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributioner som stöds som behållare värdar

- Ubuntu 14.04 LTS och 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 och 7.3
- SLES 12
- RHEL 7.2 och 7.3
- Red Hat OpenShift behållare plattform (OCP) 3.4 och 3.5
- ACS Mesosphere DC/OS 1.7.3 1.8.8
- ACS-Kubernetes 1.4.5 1.6
    - Kubernetes händelser, Kubernetes inventering och behållare processer stöds bara med version 1.4.1-45 eller senare av OMS-Agent för Linux
- ACS-Docker Swarm

### <a name="supported-windows-operating-system"></a>Windows-operativsystem som stöds

- Windows Server 2016
- Windows 10 årsdagar Edition (Professional eller Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versioner som stöds i Windows

- Docker 1.12 och 1.13
- Docker 17.03.0 och senare

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

1. Lägg till behållaren övervakning lösningen i logganalys-arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).

2. Installera och använda Docker med en OMS-agent. Baserat på operativsystemet och Docker orchestrator, kan du använda följande metoder för att konfigurera agenten.
  - För fristående värdar:
    - Installera på Linux operativsystem som stöds och kör Docker och sedan installera och konfigurera den [OMS-Agent för Linux](log-analytics-agent-linux.md).  
    - Virtuell CoreOS, det går inte att du kör OMS-Agent för Linux. I stället kan du köra en av version av OMS-Agent för Linux. Granska [Linux behållaren värdar inklusive virtuell CoreOS](#for-all-linux-container-hosts-including-coreos) eller [Azure Government Linux behållaren värdar inklusive virtuell CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) om du arbetar med behållare i Azure Government-molnet.
    - I Windows Server 2016 och Windows 10, installera Docker-motorn och klienten sedan en agent för att samla in information och skicka den till logganalys. Granska [installera och konfigurera Windows-behållaren värdar](#install-and-configure-windows-container-hosts) om du har en Windows-miljö.
  - För Docker med flera värden orchestration:
    - Om du har en miljö med Red Hat OpenShift granska [konfigurera en OMS-agent för Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Om du har ett Kubernetes-kluster med Azure Container Service:
       - Granska [konfigurera en OMS Linux-agent för Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Granska [konfigurera en Windows OMS-agent för Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Granska [Använd Helm distribuera OMS-agenten på Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Om du har ett Azure Container Service DC/OS-kluster, mer information finns i [övervaka ett Azure Container Service DC/OS-kluster med Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Om du har en miljö med Docker Swarm läge, mer information finns i [konfigurera en OMS-agent för Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Om du har ett Service Fabric-kluster, mer information finns i [övervaka behållare med OMS logganalys](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Granska de [Docker-motorn på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artikel för ytterligare information om hur du installerar och konfigurerar Docker-motorer på Windows-datorer.

> [!IMPORTANT]
> Docker måste köras **innan** du installerar den [OMS-Agent för Linux](log-analytics-agent-linux.md) på behållaren-värdar. Om du redan har installerat agenten innan du installerar Docker måste du installera om OMS-Agent för Linux. Mer information om Docker finns i [Docker webbplats](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Installera och konfigurera värdar för Linux-behållare

När du har installerat Docker, använder du följande inställningar för behållaren värden för att konfigurera agenten för användning med Docker. Du måste först ditt logganalys arbetsyte-ID och nyckel som du hittar i Azure-portalen. I arbetsytan, klickar du på **Snabbstart** > **datorer** att visa din **arbetsyte-ID** och **primärnyckel**.  Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

**För alla värdar på grund av Linux-behållaren utom virtuell CoreOS:**

- Mer information och instruktioner för hur du installerar OMS-Agent för Linux finns [ansluta Linux-datorer till logganalys](log-analytics-concept-hybrid.md).

**För alla Linux-behållaren värdar inklusive virtuell CoreOS:**

Starta den behållare som du vill övervaka. Ändra och använda följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**För alla Azure Government Linux behållaren värdar inklusive virtuell CoreOS:**

Starta den behållare som du vill övervaka. Ändra och använda följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Växlar från att använda en installerade Linux-agenten till en i en behållare**

Om du tidigare använde direkt-installerade agenten och vill använda i stället för en agent som körs i en behållare, måste du först ta bort OMS-Agent för Linux. Se [avinstallerar OMS-Agent för Linux](log-analytics-agent-linux.md) att förstå hur du avinstallera agenten.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Konfigurera en OMS-Agent för Docker Swarm

Du kan köra OMS-agenten som en global tjänst på Docker Swarm. Använd följande information för att skapa en OMS-Agent-tjänsten. Du måste ange ditt arbetsyte-ID för Log Analytics och primärnyckel.

- Kör du följande på huvudnoden.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Säker hemligheter för Docker Swarm

För Docker Swarm när hemligheten för arbetsyte-ID och primärnyckel har skapats använder du följande information för att skapa din hemliga information.

1. Kör du följande på huvudnoden.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Kontrollera att hemligheter har skapats korrekt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Kör följande kommando för att montera hemligheter av OMS-agenten.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Konfigurera en OMS-Agent för Red Hat OpenShift
Det finns tre sätt att lägga till OMS-Agent till Red Hat OpenShift att börja samla in övervakningsdata för behållaren.

* [Installera OMS-Agent för Linux](log-analytics-agent-linux.md) direkt på varje nod i OpenShift  
* [Aktivera Log Analytics VM-tillägget](log-analytics-azure-vm-extension.md) på varje nod för OpenShift som finns i Azure  
* Installera OMS-agenten som en OpenShift daemon-uppsättning  

I det här avsnittet beskriver vi de steg som krävs för att installera OMS-agenten som en OpenShift daemon-uppsättning.  

1. Logga in på noden som OpenShift och kopiera filen yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) från GitHub till din huvudnod och ändra värdet med Log Analytics arbetsyte-ID och primärnyckel.
2. Kör följande kommandon för att skapa ett projekt för Log Analytics och ange användarkontot.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Om du vill distribuera uppsättningen daemon kör du följande:

    `oc create -f ocp-omsagent.yaml`

5. Kontrollera att den är konfigurerad och fungerar korrekt, skriver du följande:

    `oc describe daemonset omsagent`  

    och resultatet bör likna:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Utför följande steg om du vill använda hemligheter för att skydda Log Analytics arbetsyte-ID och primärnyckel när du använder filen daemon set yaml OMS-Agent.

1. Logga in på noden som OpenShift och kopiera filen yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) och hemlighet genererar skript [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) från GitHub.  Det här skriptet genererar filen hemligheter yaml för Log Analytics arbetsyte-ID och primärnyckel att skydda din secrete information.  
2. Kör följande kommandon för att skapa ett projekt för Log Analytics och ange användarkontot. Den hemlighet som genererar skript begär Log Analytics arbetsyte-ID <WSID> och primärnyckel <KEY> och efter slutförande, skapar ocp-secret.yaml-filen.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Distribuera filen hemliga genom att köra följande:

    `oc create -f ocp-secret.yaml`

5. Kontrollera distributionen genom att köra följande:

    `oc describe secret omsagent-secret`  

    och resultatet bör likna:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Distribuera filen OMS-Agent daemon set yaml genom att köra följande:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Kontrollera distributionen genom att köra följande:

    `oc describe ds oms`

    och resultatet bör likna:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Konfigurera en OMS Linux-agent för Kubernetes

För Kubernetes använder du ett skript för att generera hemligheter yaml-filen för ditt arbetsyte-ID och primärnyckel att installera OMS-Agent för Linux. På den [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) , finns på filer som du kan använda med eller utan din hemliga information.

- Standard OMS-Agent för Linux DaemonSet saknar hemlig information (omsagent.yaml)
- OMS-Agent för Linux DaemonSet yaml filen används hemlig information (omsagent-ds-secrets.yaml) med hemliga generation skript för att skapa filen hemligheter yaml (omsagentsecret.yaml).

Du kan välja att skapa omsagent DaemonSets med eller utan hemligheter.

**Standardfilen OMSagent DaemonSet yaml utan hemligheter**

- OMS-agenten DaemonSet yaml standardfilen, ersätter den `<WSID>` och `<KEY>` till WSID och nyckel. Kopiera filen till din huvudnod och kör följande:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standardfilen OMSagent DaemonSet yaml med hemligheter**

1. För att använda OMS-agenten DaemonSet med hemlig information måste du först skapa hemligheterna.
    1. Kopiera skriptet och hemliga mallfilen och kontrollera att de finns i samma katalog.
        - Hemligt genererar skript - hemlighet gen.sh
        - Hemlig mall - hemlighet template.yaml
    2. Kör skriptet, som i följande exempel. Skriptet begär Log Analytics arbetsyte-ID och primärnyckel och när du har angett dem skriptet skapar en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Skapa hemligheter baljor genom att köra följande:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Du kan kontrollera genom att köra följande:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Resultatet bör likna:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Resultatet bör likna:

        ```
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

    5. Skapa din omsagent daemon set genom att köra ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Kontrollera att DaemonSet för OMS-Agent körs, liknar följande:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Använda ett skript för Kubernetes, för att generera hemligheter yaml-filen för arbetsyte-ID och primärnyckel för OMS-Agent för Linux. Använd följande exempel information med den [omsagent yaml filen](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) att skydda din hemliga information.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
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

#### <a name="configure-an-oms-windows-agent-for-kubernetes"></a>Konfigurera en Windows OMS-agent för Kubernetes
För Windows Kubernetes använder du ett skript för att generera hemligheter yaml-filen för ditt arbetsyte-ID och primärnyckel att installera OMS-Agent. På den [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) , finns på filer som du kan använda med din hemliga information.  Du måste installera OMS-agenten separat för hanterare och agent noder.  

1. Att använda OMS-agenten DaemonSet med hemlig information på huvudservern, logga in och skapa hemligheterna först.
    1. Kopiera skriptet och hemliga mallfilen och kontrollera att de finns i samma katalog.
        - Hemligt genererar skript - hemlighet gen.sh
        - Hemlig mall - hemlighet template.yaml

    2. Kör skriptet, som i följande exempel. Skriptet begär OMS arbetsyte-ID och primärnyckel och när du har angett dem skriptet skapar en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Skapa din omsagent daemon set genom att köra ``` kubectl create -f omsagentsecret.yaml ```
    4. Om du vill kontrollera, kör du följande:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Resultatet bör likna:

        ```
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

    5. Skapa din omsagent daemon set genom att köra ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Kontrollera att DaemonSet för OMS-Agent körs, liknar följande:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Om du vill installera agenten på noden Worker som kör Windows, följer du stegen i avsnittet [installera och konfigurera Windows-behållaren värdar](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Använda Helm för att distribuera OMS-Agent på Linux Kubernetes
Utför följande steg om du vill använda helm distribuera OMS-agenten på Linux Kubernetes miljön.

1. Skapa din omsagent daemon set genom att köra ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Resultatet ser ut ungefär så här:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Du kan kontrollera statusen för omsagent genom att köra: ```helm status "omsagent"``` och resultatet ser ut ungefär så här:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Mer information finns på [behållare lösning Helm diagram](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installera och konfigurera Windows-behållaren värdar

Använd informationen i avsnittet för att installera och konfigurera Windows-behållaren värdar.

#### <a name="preparation-before-installing-windows-agents"></a>Förberedelser innan du installerar Windows-agenter

Innan du installerar agenter på datorer som kör Windows, måste du konfigurera Docker-tjänsten. Konfigurationen tillåter Windows-agenten eller logganalys-tillägget för virtuell dator för att använda Docker-TCP-socket så att agenterna kan fjärransluta till Docker-daemon och för att samla in data för övervakning.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Starta Docker och verifiera konfigurationen

Det finns steg som behövs för att konfigurera TCP namngiven pipe för Windows Server:

1. Aktivera TCP pipe och namngiven pipe i Windows PowerShell.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Konfigurera Docker med konfigurationsfilen för TCP pipe och namngiven pipe. Konfigurationsfilen finns i C:\ProgramData\docker\config\daemon.json.

    I filen daemon.json behöver du följande:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Mer information om Docker daemon konfiguration används med Windows-behållare finns [Docker-motorn på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Installera Windows-agenter

Om du vill aktivera Windows- och Hyper-V behållaren övervakning, installera Microsoft Monitoring Agent (MMA) på Windows-datorer som är värdar för behållaren. Windows-datorer i din lokala miljö, se [ansluta Windows-datorer till logganalys](log-analytics-windows-agent.md). För virtuella datorer körs i Azure och Anslut dem till Log Analytics med hjälp av den [tillägg för virtuell dator](log-analytics-azure-vm-extension.md).

Du kan övervaka Windows-behållare som körs på Service Fabric. Dock endast [virtuella datorer som körs i Azure](log-analytics-azure-vm-extension.md) och [Windows-datorer i din lokala miljö](log-analytics-windows-agent.md) stöds för närvarande för Service Fabric.

Du kan kontrollera att lösningen behållaren övervakning är korrekt inställda för Windows. Om du vill kontrollera om det management pack var download korrekt, leta efter *ContainerManagement.xxx*. Filerna måste vara i mappen C:\Program Files\Microsoft övervakning Agent\Agent\Health State\Management servicepack.


## <a name="solution-components"></a>Lösningskomponenter

Från OMS-portalen navigerar du till den *lösningar galleriet* och lägga till den **lösning för övervakning av behållare**. Om du använder Windows-agenter installeras följande management pack på varje dator med en agent när du lägger till den här lösningen. Ingen konfiguration eller underhåll krävs för det management pack.

- *ContainerManagement.xxx* installerats i C:\Program Files\Microsoft övervakning Agent\Agent\Health State\Management servicepack

## <a name="container-data-collection-details"></a>Behållaren information för samlingen
Lösning för övervakning av behållare samlar in olika mått och logga prestandadata från behållaren värdar och behållare med agenter som du aktiverar.

Data samlas in varje tre minuter med följande typer av agenten.

- [OMS-Agent för Linux](log-analytics-linux-agents.md)
- [Windows-agenten](log-analytics-windows-agent.md)
- [Log Analytics VM-tillägget](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Behållaren innehåller

I följande tabell visas exempel på poster som samlas in av lösningen behållaren övervakning och datatyper som visas i sökresultaten för loggen.

| Datatyp | Datatypen i loggen Sök | Fält |
| --- | --- | --- |
| Prestanda för värdar och -behållare | `Perf` | Dator, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB minne användning MB nätverket tar emot byte, nätverket skicka byte, Processor användning s, nätverket&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållaren inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållaren image inventering | `ContainerImageInventory` | TimeGenerated, dator, bild, ImageTag, ImageSize, VirtualSize, körs, pausas, stoppas, misslyckades, SourceSystem, ImageID, TotalContainer |
| Behållaren logg | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, behållarnamn LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Behållaren loggfiler | `ContainerServiceLog`  | TimeGenerated, dator, TimeOfCommand, bild, kommandot, SourceSystem, behållar-ID |
| Behållaren nod inventering | `ContainerNodeInventory_CL`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes inventering | `KubePodInventory_CL` | TimeGenerated, dator, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Behållaren process | `ContainerProcess_CL` | TimeGenerated, dator, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes händelser | `KubeEvents_CL` | TimeGenerated, dator, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, meddelande |

Etiketter som läggs till *PodLabel* datatyper är egna etiketter. Tillagda PodLabel etiketterna visas i tabellen är exempel. Därför `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` kommer skiljer sig åt i din miljö datauppsättning och allmänt likna `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Övervaka behållare
När du har aktiverat i logganalys-portalen lösningen i **behållare** panelen visar sammanfattningsinformation om behållaren-värdar och behållare som körs på värdar.


![Behållare sida vid sida](./media/log-analytics-containers/containers-title.png)

Panelen visar en översikt över hur många behållare som du har i miljön och om de är misslyckades, igång eller stoppad.

### <a name="using-the-containers-dashboard"></a>Med hjälp av instrumentpanelen behållare
Klicka på den **behållare** panelen. Därifrån ser du vyer som är ordnad efter:

- **Behållaren händelser** – visar status för behållaren och datorer med misslyckade behållare.
- **Loggar för behållaren** – visar ett diagram över behållaren loggfiler som skapas med tiden och en lista med datorer med det högsta antalet loggfiler.
- **Kubernetes händelser** -visas ett diagram över Kubernetes händelser som genererats med tiden och en lista över orsaker varför skida genererade händelser. *Den här datamängden används endast i Linux-miljöer.*
- **Kubernetes Namespace inventering** – visar antalet namnområden och skida och deras hierarki. *Den här datamängden används endast i Linux-miljöer.*
- **Behållaren nod inventering** -visar antalet orchestration-typer som används på behållaren noder/värdar. Datorn noder/värdarna visas också av behållare numret. *Den här datamängden används endast i Linux-miljöer.*
- **Behållaren bilder inventering** -visar totalt antal behållare bilder som används och antalet bildtyper. Antalet avbildningar som också anges med bildtaggen.
- **Behållare Status** -och visar det totala antalet behållare noder/värddatorer behållare som körs. Datorer visas också av antalet värdar som körs.
- **Behållaren processen** – visar ett linjediagram för behållaren processer som körs över tid. Behållare visas också genom att köra kommandot/processen i behållare. *Den här datamängden används endast i Linux-miljöer.*
- **Behållaren CPU-prestanda** – visar ett linjediagram av den genomsnittliga processoranvändningen över tid för datorn är noder/värd. Även baserat listor datorn noder/värdar på Genomsnittlig CPU-användning.
- **Behållaren minnesprestanda** – visar ett linjediagram minnesanvändningen över tid. Visar också minne användning baserat på instansnamn.
- **Datorprestanda** -visar linjediagram för procent av CPU-prestanda över tiden, procent av minnesanvändning över tid och MB ledigt diskutrymme över tid. Du kan hovra över en rad i ett schema för att visa mer information.


Varje område av instrumentpanelen är en bild av en sökning som körs på insamlade data.

![Behållare instrumentpanelen](./media/log-analytics-containers/containers-dash01.png)

![Behållare instrumentpanelen](./media/log-analytics-containers/containers-dash02.png)

I den **behållare Status** området klickar du på ytan, enligt nedan.

![Behållare status](./media/log-analytics-containers/containers-status.png)

Sök i loggfilen öppnas och visar information om tillståndet för en behållare.

![Logga Sök efter behållare](./media/log-analytics-containers/containers-log-search.png)

Härifrån kan redigera du frågan om du vill ändra för att hitta informationen du är intresserad av. Mer information om loggen sökningar finns [logga sökningar i logganalys](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Felsöka genom att söka efter en misslyckad behållare

Logganalys markerar en behållare som **misslyckades** om den har avslutats med slutkoden noll. Du kan se en översikt över fel och problem i miljön i den **misslyckades behållare** område.

### <a name="to-find-failed-containers"></a>Misslyckades att hitta behållare
1. Klicka på den **behållare Status** område.  
   ![Behållare status](./media/log-analytics-containers/containers-status.png)
2. Sök i loggfilen öppnas och visar tillståndet för behållarna som liknar följande.  
   ![behållare tillstånd](./media/log-analytics-containers/containers-log-search.png)
3. Klicka sedan på aggregerade värdet för misslyckade behållarna för att visa ytterligare information. Expandera **visa fler** att visa det bild-ID.  
   ![misslyckade behållare](./media/log-analytics-containers/containers-state-failed.png)  
4. Därefter skriver du följande i frågan. `ContainerInventory <ImageID>` du vill se information om avbildningen som avbildningens storlek och antal bilder har stoppats och misslyckade.  
   ![misslyckade behållare](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Sökloggar för behållardata
När du felsöker ett visst fel hjälper det för att se om det förekommer i din miljö. Följande typer av loggen kan du skapa frågor för att returnera information som du vill använda.


- **ContainerImageInventory** – Använd den här typen när du försöker att hitta information som är ordnad efter bilden och för att visa bildinformation som bild-ID: N eller storlekar.
- **ContainerInventory** – Använd den här typen om du vill ha information om behållarens plats deras namn är och vad de kör bilder.
- **ContainerLog** – Använd den här typen om du vill söka efter specifika informationen i felloggen och transaktioner.
- **ContainerNodeInventory_CL** Använd den här typen om du vill ha information om värden/nod där behållare finns. Det ger dig Docker-version, orchestration-typ., lagring och nätverksinformation.
- **ContainerProcess_CL** använda den här typen för att snabbt se den process som körs i behållaren.
- **ContainerServiceLog** – Använd den här typen när du försöker att hitta information om redovisningsspårning för Docker-daemon, till exempel starta, stoppa, ta bort eller pull-kommandon.
- **KubeEvents_CL** Använd den här typen om du vill se Kubernetes händelser.
- **KubePodInventory_CL** Använd den här typen om du vill förstå hierarkiinformation för klustret.


### <a name="to-search-logs-for-container-data"></a>Att söka i loggar för behållardata
* Välj en bild som du vet misslyckades nyligen och hitta felloggarna för den. Börja med att hitta ett behållarnamn som körs på avbildningen med en **ContainerInventory** sökning. Till exempel söka efter `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Sök efter Ubuntu behållare](./media/log-analytics-containers/search-ubuntu.png)

  Namnet på behållaren bredvid **namn**, och Sök efter dessa loggar. I det här exemplet är det `ContainerLog | where Name == "cranky_stonebreaker"`.

**Visa information om prestanda**

När du börjat skapa frågor, hjälper det för att se vad du kan göra först. Försök till exempel en bred fråga om du vill se alla prestandadata genom att skriva följande sökfråga.

```
Perf
```

![behållare prestanda](./media/log-analytics-containers/containers-perf01.png)

Du kan definiera de prestandadata som det uppstår till en viss behållare genom att skriva namnet på den till höger om din fråga.

```
Perf <containerName>
```

Som visar en lista över prestandamått som samlas in för en enskild behållare.

![behållare prestanda](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Exempel loggen sökfrågor
Det vara bra att skapa frågor som börjar med ett exempel eller två och ändra dem så att de passar din miljö. Som en startpunkt som du kan experimentera med den **exempelfrågor** område för att hjälpa dig att skapa mer avancerade frågor.

![Behållare frågor](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Spara loggfilen sökresultat
Spara frågor är en funktion som standard i logganalys. Genom att spara dem, har du de som du har hittat användbar praktiska för framtida användning.

När du skapar en fråga som vara användbara sparar du genom att klicka på **Favoriter** överst på sidan logga. Du kan enkelt använda det senare från den **min instrumentpanel** sidan.

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad behållaren dataposter.
