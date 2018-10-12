---
title: Lösning för övervakning av behållare i Azure Log Analytics | Microsoft Docs
description: Lösning för övervakning av behållare i Log Analytics hjälper dig att visa och hantera Docker- och Windows behållare-värdar i en enda plats.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: f9876f3e21a7cfccae2fb7f70913269d4ca1fdf4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115377"
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Lösning för övervakning av behållare i Log Analytics

![Symbol för behållare](./media/log-analytics-containers/containers-symbol.png)

Den här artikeln beskrivs hur du konfigurerar och använder övervakningslösningen för behållare i Log Analytics, som hjälper dig att visa och hantera Docker- och Windows behållare-värdar i en enda plats. Docker är ett system för virtualisering av programvara som används för att skapa behållare som kan automatiserar distribution av programvara till sin IT-infrastruktur.

Lösningen visar vilka behållare som körs, vilka behållaravbildning de körs och där behållare som körs. Du kan visa detaljerad granskning av information som visar kommandon som används med behållare. Och du kan felsöka behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrvisa Docker eller Windows-värdar. Du kan hitta behållare som kan vara mycket brus och konsumerande ytterligare resurser på en värd. Och du kan visa centraliserad processor, minne, lagring och nätverksinformation om användning och prestanda för behållare. På datorer som kör Windows kan du centralisera och jämföra loggar från Windows Server, Hyper-V och Docker-behållare. Lösningen stöder behållardirigeringsverktygen för följande:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Om du vill övervaka prestanda för dina arbetsbelastningar som distribueras till Kubernetes-miljöer finns på Azure Kubernetes Service (AKS), se [övervaka Azure Kubernetes Service](../monitoring/monitoring-container-health.md). Övervakningslösningen för behållaren omfattar inte support för att övervaka den plattformen.  

Följande diagram visar relationerna mellan olika behållare-värdar och -agenter med Log Analytics.

![Behållare-diagram](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systemkrav och plattformar som stöds

Innan du börjar ska du granska följande information för att kontrollera att du uppfyller kraven.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Lösning för övervakning av behållare som har stöd för Docker Orchestrator och OS-plattform
I följande tabell beskrivs Docker orchestration och operativsystemet övervakningsstöd för behållaren inventering, prestanda och loggar med Log Analytics.   

| | ACS | Linux | Windows | Container<br>Inventering | Bild<br>Inventering | Node<br>Inventering | Container<br>Prestanda | Container<br>Händelse | Händelse<br>Logga | Container<br>Logga |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Tjänst<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Skift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(fristående) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>(fristående) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Docker-versioner som stöds i Linux

- Docker 1.11 1.13
- Docker CE och EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributioner som stöds som behållare-värdar

- Ubuntu 14.04 LTS och 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE 42.2 LEAP
- CentOS 7.2 och 7.3
- SLES 12
- RHEL 7.2 och 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 och 3.5
- ACS Mesosphere DC/OS 1.7.3 1.8.8
- ACS-Kubernetes 1.4.5 1.6
    - Kubernetes-händelser, Kubernetes inventering och behållaren processer stöds endast med version 1.4.1-45 eller senare av OMS-agenten för Linux
- ACS-Docker Swarm

### <a name="supported-windows-operating-system"></a>Windows-operativsystem som stöds

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional eller Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versioner som stöds på Windows

- Docker 1.12 och 1.13
- Docker 17.03.0 och senare

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

1. Lägg till lösning för övervakning av behållare till Log Analytics-arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md).

2. Installera och använda Docker med en OMS-agent. Baserat på ditt operativsystem och Docker orchestrator kan använda du följande metoder för att konfigurera ditt ombud.
  - För fristående värdar:
    - Installera på Linux operativsystem som stöds och kör Docker och sedan installera och konfigurera den [OMS-agenten för Linux](log-analytics-agent-linux.md).  
    - På CoreOS, kan inte du köra OMS-agenten för Linux. I stället kan du köra en behållare version av OMS-agenten för Linux. Granska [Linux behållare-värdar, inklusive CoreOS](#for-all-linux-container-hosts-including-coreos) eller [Azure Government Linux behållare-värdar, inklusive CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) om du arbetar med behållare i Azure Government-molnet.
    - Installera Docker-motorn och klienten i Windows Server 2016 och Windows 10, sedan en agent för att samla in information och skicka den till Log Analytics. Granska [installera och konfigurera Windows behållarvärdar](#install-and-configure-windows-container-hosts) om du har en Windows-miljö.
  - För dirigering av Docker flera värden:
    - Om du har en Red Hat OpenShift-miljö kan du granska [konfigurera en OMS-agent för Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Om du har ett Kubernetes-kluster med hjälp av Azure Container Service:
       - Granska [konfigurera en OMS Linux-agent för Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Granska [konfigurera en OMS-Windows-agent för Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Granska [Använd Helm för att distribuera OMS-agenten på Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Om du har ett Azure Container Service DC/OS-kluster kan du läsa mer på [övervaka ett Azure Container Service DC/OS-kluster med Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Om du har en miljö på Docker Swarm-läge, Läs mer på [konfigurera en OMS-agent för Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Om du har Service Fabric-kluster kan du läsa mer på [övervaka behållare med OMS Log Analytics](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Granska den [Docker-motorn på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artikeln för mer information om hur du installerar och konfigurerar Docker-motorer på datorer som kör Windows.

> [!IMPORTANT]
> Docker måste köras **innan** du installerar den [OMS-agenten för Linux](log-analytics-agent-linux.md) på behållare-värdar. Om du redan har installerat agenten innan du installerar Docker måste du installera om OMS-agenten för Linux. Mer information om Docker finns i den [Docker webbplats](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Installera och konfigurera Linux-behållare-värdar

När du har installerat Docker, kan du använda följande inställningar för dina behållare värden för att konfigurera agenten för användning med Docker. Först måste din Log Analytics arbetsyte-ID och nyckel, som du hittar i Azure-portalen. På arbetsytan och klicka på **Snabbstart** > **datorer** att visa dina **arbetsyte-ID** och **primärnyckel**.  Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

**För alla värdar på grund av Linux-behållare utom CoreOS:**

- Mer information och anvisningar om hur du installerar OMS-agenten för Linux finns i [Anslut dina Linux-datorer till Log Analytics](log-analytics-concept-hybrid.md).

**För alla Linux-behållare-värdar, inklusive CoreOS:**

Starta den behållare som du vill övervaka. Ändra och använda följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**För alla Azure Government Linux-behållare-värdar, inklusive CoreOS:**

Starta den behållare som du vill övervaka. Ändra och använda följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Växla från att använda en installerad agent för Linux till någon i en behållare**

Om du tidigare använde direkt installerade agenten och vill använda i stället för en agent som körs i en behållare, måste du först ta bort OMS-agenten för Linux. Se [avinstallera OMS-agenten för Linux](log-analytics-agent-linux.md) vill lära dig att avinstallera agenten.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Konfigurera en OMS-Agent för Docker Swarm

Du kan köra OMS-agenten som en global tjänst på Docker Swarm. Använd följande information för att skapa en OMS-Agent-tjänsten. Du måste ange din Log Analytics arbetsyte-ID och den primärnyckeln.

- Kör du följande på den överordnade noden.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Säker hemligheter för Docker Swarm

För Docker Swarm, när hemligheten för arbetsyte-ID och den primärnyckeln har skapats använder du följande information för att skapa din hemliga information.

1. Kör du följande på den överordnade noden.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Kontrollera att hemligheter skapades på rätt sätt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Kör följande kommando för att montera hemligheter för behållare OMS-agenten.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Konfigurera en OMS-Agent för Red Hat OpenShift
Det finns tre sätt att lägga till OMS-agenten i Red Hat OpenShift börjar samla in övervakningsdata för behållaren.

* [Installera OMS-agenten för Linux](log-analytics-agent-linux.md) direkt på varje nod för OpenShift  
* [Aktivera VM-tillägg för Log Analytics](log-analytics-azure-vm-extension.md) på varje nod för OpenShift som finns i Azure  
* Installera OMS-agenten som en daemon-uppsättning OpenShift  

I det här avsnittet beskriver vi de steg som krävs för att installera OMS-agenten som en daemon-set OpenShift.  

1. Logga in på noden som OpenShift och kopiera filen yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) från GitHub till den överordnade noden och ändra värdet med Log Analytics arbetsyte-ID och den primärnyckeln.
2. Kör följande kommandon för att skapa ett projekt för Log Analytics och ange användarkontot.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Om du vill distribuera daemon-set, kör du följande:

    `oc create -f ocp-omsagent.yaml`

5. Kontrollera att den är konfigurerad och fungerar som den ska, skriver du följande:

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

Utför följande steg om du vill använda hemligheter för att skydda din Log Analytics arbetsyte-ID och den primärnyckeln när du använder OMS-agenten daemon-set yaml-fil.

1. Logga in på noden som OpenShift och kopiera filen yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) och hemlighet som genererar skript [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) från GitHub.  Det här skriptet genererar hemligheter yaml-fil för Log Analytics arbetsyte-ID och primärnyckel att skydda din secrete information.  
2. Kör följande kommandon för att skapa ett projekt för Log Analytics och ange användarkontot. Hemligheten som genererar skript frågar för Log Analytics arbetsyte-ID <WSID> och den primärnyckeln <KEY> och när åtgärden har slutförts skapas filen ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Distribuera hemlighetsfilen genom att köra följande:

    `oc create -f ocp-secret.yaml`

5. Verifiera distributionen genom att köra följande:

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

6. Distribuera OMS-agenten daemon-set yaml-fil genom att köra följande:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Verifiera distributionen genom att köra följande:

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

För Kubernetes använder du ett skript för att generera hemligheter yaml-fil för ditt arbetsyte-ID och den primärnyckeln du installerar OMS-agenten för Linux. På den [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) sidan finns det filer som du kan använda med eller utan din hemliga information.

- Standard OMS-agenten för Linux DaemonSet har inte hemlig information (omsagent.yaml)
- OMS-agenten för Linux DaemonSet yaml-fil använder hemlig information (omsagent-ds-secrets.yaml) med hemliga generation skript för att generera filen hemligheter yaml (omsagentsecret.yaml).

Du kan välja att skapa omsagent DaemonSets med eller utan hemligheter.

**Standard OMSagent DaemonSet yaml-fil utan hemligheter**

- För standard OMS-agenten DaemonSet yaml-fil ersätter den `<WSID>` och `<KEY>` till WSID och nyckel. Kopiera filen till din-huvudnod och kör du följande:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standard OMSagent DaemonSet yaml-fil med hemligheter**

1. För att använda OMS-agenten DaemonSet med hemlig information måste du först skapa hemligheterna.
    1. Kopiera skriptet och hemliga mallfilen och kontrollera att de finns i samma katalog.
        - Hemligheten som genererar skript - hemlighet gen.sh
        - Hemlig mall - hemlighet template.yaml
    2. Kör skriptet, som i följande exempel. Skriptet begär Log Analytics arbetsyte-ID och den primärnyckeln och när du har angett dem skriptet skapar en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Skapa hemligheter pod genom att köra följande:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Du kan kontrollera genom att köra följande:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Utdata bör likna:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Utdata bör likna:

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

    5. Skapa din omsagent daemon-set genom att köra ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Kontrollera att OMS-agenten DaemonSet körs, ungefär så här:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Använda ett skript för att generera hemligheter yaml-fil för arbetsyte-ID och den primärnyckeln för OMS-agenten för Linux för Kubernetes. Använd följande exempelinformation med den [omsagent yaml-fil](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) att skydda din hemliga information.

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

#### <a name="configure-an-oms-windows-agent-for-kubernetes"></a>Konfigurera en OMS-Windows-agent för Kubernetes
För Windows Kubernetes använder du ett skript för att generera hemligheter yaml-fil för ditt arbetsyte-ID och den primärnyckeln att installera OMS-agenten. På den [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) sidan finns det filer som du kan använda med din hemliga information.  Du måste installera OMS-agenten separat för master- och agentnoderna.  

1. Att använda OMS-agenten DaemonSet med hemlig information på Huvudmålservern nod, logga in och skapa hemligheterna först.
    1. Kopiera skriptet och hemliga mallfilen och kontrollera att de finns i samma katalog.
        - Hemligheten som genererar skript - hemlighet gen.sh
        - Hemlig mall - hemlighet template.yaml

    2. Kör skriptet, som i följande exempel. Skriptet begär ID för OMS-arbetsyta och primärnyckel och när du har angett dem skriptet skapar en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Skapa din omsagent daemon-set genom att köra ``` kubectl create -f omsagentsecret.yaml ```
    4. Du kan kontrollera genom att köra följande:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Utdata bör likna:

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

    5. Skapa din omsagent daemon-set genom att köra ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Kontrollera att OMS-agenten DaemonSet körs, ungefär så här:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Om du vill installera agenten på den Worker-nod som kör Windows, följer du stegen i avsnittet [installera och konfigurera Windows behållarvärdar](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Använd Helm för att distribuera OMS-agenten på Linux Kubernetes
Om du vill använda helm för att distribuera OMS-agenten på Linux Kubernetes-miljön, utför du följande steg.

1. Skapa din omsagent daemon-set genom att köra ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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
3. Du kan kontrollera status för omsagent genom att köra: ```helm status "omsagent"``` och utdata ser ut ungefär som följande:

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
Mer information finns på [behållare lösning Helm-diagrammet](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installera och konfigurera Windows-behållare-värdar

Använd informationen i avsnittet för att installera och konfigurera Windows-behållare-värdar.

#### <a name="preparation-before-installing-windows-agents"></a>Förberedelse innan du installerar Windows-agenter

Innan du installerar agenter på datorer som kör Windows, måste du konfigurera Docker-tjänsten. Konfigurationen tillåter Windows-agenten eller Log Analytics-tillägget för virtuell dator för att använda Docker-TCP-socket så att agenterna kan komma åt Docker-daemon via en fjärranslutning och för att samla in data för övervakning.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Starta Docker och verifiera konfigurationen

Det finns steg som krävs för att ställa in TCP namngiven pipe för Windows Server:

1. Aktivera TCP pipe och namngiven pipe i Windows PowerShell.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Konfigurera Docker med konfigurationsfilen för TCP pipe och namngiven pipe. Konfigurationsfilen finns på C:\ProgramData\docker\config\daemon.json.

    I filen daemon.json behöver du följande:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Mer information om konfigurationen av Docker-daemon med Windows-behållare finns i [Docker-motorn på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Installera Windows-agenter

Om du vill aktivera behållarövervakning för Windows och Hyper-V, installera Microsoft Monitoring Agent (MMA) på Windows-datorer som är behållare-värdar. Datorer som kör Windows i din lokala miljö, se [ansluta Windows-datorer till Log Analytics](log-analytics-windows-agent.md). För virtuella datorer som körs i Azure och Anslut dem till Log Analytics med hjälp av den [tillägg för virtuell dator](log-analytics-azure-vm-extension.md).

Du kan övervaka Windows-behållare som körs på Service Fabric. Dock endast [virtuella datorer som körs i Azure](log-analytics-azure-vm-extension.md) och [datorer som kör Windows i din lokala miljö](log-analytics-windows-agent.md) stöds för närvarande för Service Fabric.

Du kan kontrollera att övervakningslösningen för behållare är korrekt inställda för Windows. Du kan kontrollera om det management pack var download korrekt genom att leta efter *ContainerManagement.xxx*. Filerna måste vara i mappen C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management servicepack.


## <a name="solution-components"></a>Lösningskomponenter

Från OMS-portalen navigerar du till den *lösningsgalleriet* och lägga till den **lösning för övervakning av behållare**. Om du använder Windows-agenter kan installeras följande management pack på varje dator med en agent när du lägger till den här lösningen. Ingen konfigurering eller underhåll krävs för management pack.

- *ContainerManagement.xxx* installerats i C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management servicepack

## <a name="container-data-collection-details"></a>Samling data-behållarinformation
Behållarövervakning lösningen samlar in olika mått och loggfiler prestandadata från behållare-värdar och behållare med hjälp av agenter som du aktiverar.

Data samlas in var tredje minut av följande typer av agenten.

- [OMS-Agent för Linux](log-analytics-linux-agents.md)
- [Windows-agenten](log-analytics-windows-agent.md)
- [Log Analytics VM-tillägg](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Behållarposter

I följande tabell visas exempel på poster som samlas in av övervakningslösningen för behållare och vilka datatyper av som visas i sökresultaten för loggen.

| Datatyp | Datatypen i Loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Datorn, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB, MB för användning av minne, nätverk ta emot byte, nätverk skicka byte, Processor användning sek, Network&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållare-inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållaravbildningar | `ContainerImageInventory` | TimeGenerated, dator, bild, ImageTag, ImageSize, VirtualSize, drift, pausad, stoppas, misslyckades, SourceSystem, ImageID, TotalContainer |
| Behållarloggen | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, namn, LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Container service-logg | `ContainerServiceLog`  | TimeGenerated, dator, TimeOfCommand, bild, kommandot, SourceSystem, behållar-ID |
| Behållarnodslager | `ContainerNodeInventory_CL`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-inventering | `KubePodInventory_CL` | TimeGenerated, dator, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Behållarprocess | `ContainerProcess_CL` | TimeGenerated, dator, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-händelser | `KubeEvents_CL` | TimeGenerated, dator, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, meddelande |

Etiketter läggs till *PodLabel* datatyper är egna etiketter. Tillagda PodLabel etiketterna visas i tabellen är exempel. Därför `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` kommer skiljer sig åt i datauppsättningen för din miljö och allmänt likna `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Övervaka containrar
När du har aktiverat i Log Analytics-portalen lösning den **behållare** panel visar sammanfattningsinformation om din behållare-värdar och behållare som körs på värdar.


![Panel för behållare](./media/log-analytics-containers/containers-title.png)

Panelen visar en översikt över hur många behållare som du har i miljön och om de är misslyckades, körs eller är stoppad.

### <a name="using-the-containers-dashboard"></a>Med hjälp av instrumentpanelen för behållare
Klicka på den **behållare** panelen. Därifrån ser vyer som är ordnade efter:

- **Behållarhändelser** – visar status för container och datorer med misslyckade behållare.
- **Behållarloggar** – visar ett diagram över behållare loggfiler som skapas med tiden och en lista över datorer med det högsta antalet loggfiler.
- **Kubernetes-händelser** -ett diagram med Kubernetes-händelser som genererats över tid och en lista över orsaker varför poddar genereras händelserna. *Den här datamängden används endast i Linux-miljöer.*
- **Kubernetes Namespace inventering** – visar antalet namnområden och poddar och visar deras hierarki. *Den här datamängden används endast i Linux-miljöer.*
- **Behållarnodslager** -visar antalet orchestration-typer som används på noder/behållarvärdar. Noder/värdar för datorn visas också av antalet behållare. *Den här datamängden används endast i Linux-miljöer.*
- **Inventering av Behållaravbildningar** -visar det totala antalet behållaravbildningar som används och antal avbildningstyper. Antalet avbildningar som också anges med bildtaggen.
- **Behållarstatus** -och visar det totala antalet behållare noder/värddatorer behållare som körs. Datorer visas också av antalet värdar som körs.
- **Behållarprocess** – visar ett linjediagram med behållare processer som körs med tiden. Behållare visas också genom att köra kommandot/bearbeta i behållare. *Den här datamängden används endast i Linux-miljöer.*
- **Processorprestanda för behållare** – visar ett linjediagram med den genomsnittliga CPU-belastningen över tid för dator-noder /-värdar. Även baserat listor datorn noder/värdar på den genomsnittliga CPU-användning.
- **Minnesprestanda för behållare** – visar ett linjediagram med minnesanvändning över tid. Visas även datorminne användning baserat på instansnamn.
- **Datorprestanda** -visar linjediagram för procent av CPU-prestanda över tid, procent av minnesanvändning över tid och mängden ledigt diskutrymme över tid. Du kan hovra över någon av staplarna i ett diagram om du vill visa mer information.


Varje område av instrumentpanelen är en visuell representation av en sökning som körs på insamlade data.

![Behållarinstrumentpanelen](./media/log-analytics-containers/containers-dash01.png)

![Behållarinstrumentpanelen](./media/log-analytics-containers/containers-dash02.png)

I den **Behållarstatus** området klickar du på det översta området som visas nedan.

![Behållarstatus](./media/log-analytics-containers/containers-status.png)

Loggsökning öppnas och visar information om tillståndet för dina behållare.

![Loggsökning för behållare](./media/log-analytics-containers/containers-log-search.png)

Härifrån kan redigera du frågan om du vill ändra den för att hitta informationen du är intresserad av. Läs mer om Loggsökningar [Loggsökningar i Log Analytics](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Felsöka genom att söka efter en misslyckad behållare

Log Analytics markerar en behållare som **misslyckades** om den har avslutats med en slutkod som inte är noll. Du kan se en översikt över fel och problem i miljön i den **misslyckades behållare** området.

### <a name="to-find-failed-containers"></a>Att hitta misslyckade behållare
1. Klicka på den **Behållarstatus** området.  
   ![Behållarstatus](./media/log-analytics-containers/containers-status.png)
2. Loggsökning öppnas och visar tillståndet för dina behållare som liknar följande.  
   ![tillstånd för behållare](./media/log-analytics-containers/containers-log-search.png)
3. Klicka sedan på sammanlagt värde för misslyckade behållarna för att visa ytterligare information. Expandera **visa fler** att visa avbildnings-ID.  
   ![misslyckade behållare](./media/log-analytics-containers/containers-state-failed.png)  
4. Därefter skriver du följande i sökfrågan. `ContainerInventory <ImageID>` för att se information om avbildningen som avbildningens storlek och antalet stoppad och misslyckade bilder.  
   ![misslyckade behållare](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Sök i loggar för behållardata
När du felsöker ett specifikt fel, kan det se var det inträffar i din miljö. Följande loggtyper av kan du skapa frågor för att returnera information som du vill.


- **ContainerImageInventory** – Använd den här typen när du försöker att hitta information som är ordnade efter avbildningen och för att visa bildinformation som bild-ID: N eller storlekar.
- **ContainerInventory** – Använd den här typen om du vill ha information om behållarens plats deras namn är och vad avbildningar som de körs.
- **ContainerLog** – Använd den här typen om du vill söka efter specifika fel logginformation och poster.
- **ContainerNodeInventory_CL** Använd den här typen om du vill ha information om värd/nod där behållare är bosatta. Den ger dig Docker version, orchestration-typ., lagring och nätverksinformation.
- **ContainerProcess_CL** använda den här typen för att snabbt se den process som körs i behållaren.
- **ContainerServiceLog** – Använd den här typen när du försöker att hitta information om redovisningsspårning för Docker-daemon, till exempel starta, stoppa, ta bort eller pull-kommandon.
- **KubeEvents_CL** använder den här typen om du vill visa Kubernetes-händelser.
- **KubePodInventory_CL** Använd den här typen om du vill förstå klusterinformationen för hierarkin.


### <a name="to-search-logs-for-container-data"></a>Att söka i loggar för behållardata
* Välj en avbildning som du vet har nyligen misslyckats och hitta felloggarna för den. Starta genom att söka efter ett behållarnamn som kör avbildningen med en **ContainerInventory** sökning. Till exempel söka efter `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Sök efter Ubuntu behållare](./media/log-analytics-containers/search-ubuntu.png)

  Namnet på behållaren bredvid **namn**, och Sök efter dessa loggar. I det här exemplet är det `ContainerLog | where Name == "cranky_stonebreaker"`.

**Visa information om prestanda**

När du börjar skapa frågor, kan det se vad som är möjligt först. Om du vill se alla prestandadata, Prova till exempel en bred fråga genom att skriva följande sökvillkor.

```
Perf
```

![prestanda för behållare](./media/log-analytics-containers/containers-perf01.png)

Du kan begränsa de prestandadata som du ser att en specifik behållare genom att skriva namnet på den till höger om din fråga.

```
Perf <containerName>
```

Som visar en lista över prestandamått som samlas in för en enskild behållare.

![prestanda för behållare](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Exempel loggsökningsfrågor
Det är ofta bra att skapa frågor som börjar med ett exempel eller två och ändra dem så att de passar din miljö. Som en startpunkt som du kan experimentera med den **exempelfrågor** område för att hjälpa dig att skapa mer avancerade frågor.

![Behållare-frågor](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Sparar loggsökningsfrågor
Spara frågor är en standard funktion i Log Analytics. Genom att spara dem, har du de som du har hittat användbara praktiskt för framtida användning.

När du skapar en fråga som användbara kan du spara det genom att klicka på **Favoriter** överst på sidan Log Search. Du kan enkelt använda det senare från den **min instrumentpanel** sidan.

## <a name="next-steps"></a>Nästa steg
* [Söka loggarna](log-analytics-log-searches.md) att visa detaljerade data behållarposter.
