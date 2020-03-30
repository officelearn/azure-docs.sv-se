---
title: Lösning för behållarövervakning i Azure Monitor | Microsoft-dokument
description: Container Monitoring-lösningen i Azure Monitor hjälper dig att visa och hantera dina Docker- och Windows-behållarvärdar på en enda plats.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664702"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Lösning för behållarövervakning i Azure Monitor

![Symbol för behållare](./media/containers/containers-symbol.png)

I den här artikeln beskrivs hur du konfigurerar och använder containerövervakningslösningen i Azure Monitor, som hjälper dig att visa och hantera dina Docker- och Windows-behållarvärdar på en enda plats. Docker är ett programvaruvirtualiseringssystem som används för att skapa behållare som automatiserar programdistribution till sin IT-infrastruktur.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Lösningen visar vilka behållare som körs, vilken behållaravbildning de kör och var behållarna körs. Du kan visa detaljerad granskningsinformation som visar kommandon som används med behållare. Och du kan felsöka behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrvisa Docker- eller Windows-värdar. Du kan hitta behållare som kan vara bullriga och förbrukar överskjutande resurser på en värd. Och du kan visa centraliserad cpu, minne, lagring och nätverksanvändning och prestandainformation för behållare. På datorer som kör Windows kan du centralisera och jämföra loggar från Windows Server-, Hyper-V- och Docker-behållare. Lösningen stöder följande behållare orchestrators:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Om du har behållare som distribueras i [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)rekommenderar vi att du aktiverar både Service [Fabric-lösningen](../../service-fabric/service-fabric-diagnostics-oms-setup.md) och den här lösningen för att inkludera övervakning av klusterhändelser. Innan du aktiverar Service Fabric-lösningen bör du granska [Använda Service Fabric-lösningen](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) för att förstå vad den tillhandahåller och hur den ska användas.

Om du är intresserad av att övervaka prestanda för dina arbetsbelastningar som distribueras till Kubernetes-miljöer som finns på Azure Kubernetes Service (AKS), se [Övervaka Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md). Container Monitoring-lösningen stöder inte övervakning av den plattformen.  

Följande diagram visar relationerna mellan olika behållarvärdar och agenter med Azure Monitor.

![Diagram över behållare](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systemkrav och plattformar som stöds

Innan du börjar bör du granska följande information för att verifiera att du uppfyller kraven.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Stöd för containerövervakningslösning för Docker Orchestrator och OS-plattform

I följande tabell beskrivs Docker-orkestrering och operativsystemets övervakningsstöd för behållarinventering, prestanda och loggar med Azure Monitor.   

| | ACS | Linux | Windows | Container<br>Inventering | Bild<br>Inventering | Node<br>Inventering | Container<br>Prestanda | Container<br>Händelse | Händelse<br>Logga | Container<br>Logga |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosfär<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Svärm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Tjänst<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Röd hatt öppen<br>Skift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(fristående) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>(fristående) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Docker-versioner som stöds på Linux

- Docker 1,11 till 1,13
- Docker CE och EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributioner stöds som behållarvärdar

- Ubuntu 14.04 LTS och 16.04 LTS
- CoreOS(stabil)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7,2 och 7,3
- SLES 12 (PÅ ANDRA)
- RHEL 7.2 och 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 och 3.5
- ACS Mesosphere DC/OS 1.7.3 till 1.8.8
- ACS Kubernetes 1.4.5 till 1.6
    - Kubernetes-händelser, Kubernetes-lager och behållarprocesser stöds endast med version 1.4.1-45 och senare av Log Analytics-agenten för Linux
- ACS Docker Svärm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Windows-operativsystem som stöds

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional eller Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versioner som stöds i Windows

- Docker 1.12 och 1.13
- Docker 17.03.0 och senare

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Använd följande information för att installera och konfigurera lösningen.

1. Lägg till lösningen för behållarövervakning i din Log Analytics-arbetsyta från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) eller genom att använda processen som beskrivs i Lägg [till övervakningslösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).

2. Installera och använd Docker med en Log Analytics-agent. Baserat på ditt operativsystem och Docker orchestrator kan du använda följande metoder för att konfigurera din agent.
   - För fristående värdar:
     - På Linux-operativsystem som stöds installerar och kör Du docker och installerar och konfigurerar sedan [Log Analytics-agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - På CoreOS kan du inte köra Log Analytics-agenten för Linux. I stället kör du en containerversion av Log Analytics-agenten för Linux. Granska Linux-behållarvärdar, inklusive CoreOS- eller Azure Government Linux-behållare, inklusive CoreOS om du arbetar med behållare i Azure Government Cloud.
     - Installera Docker-motorn och klienten på Windows Server 2016 och Windows 10 och anslut sedan en agent för att samla in information och skicka den till Azure Monitor. Granska [Installera och konfigurera Windows-behållarvärdar](#install-and-configure-windows-container-hosts) om du har en Windows-miljö.
   - För Docker multi-host orkestrering:
     - Om du har en Red Hat OpenShift-miljö läser du Konfigurera en Log Analytics-agent för Red Hat OpenShift.
     - Om du har ett Kubernetes-kluster med Azure Container Service:
       - Granska [Konfigurera en Log Analytics Linux-agent för Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Granska [Konfigurera en Log Analytics Windows-agent för Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Granska Använd Helm för att distribuera Log Analytics-agent på Linux Kubernetes.
     - Om du har ett DC/OS-kluster för Azure Container Service kan du läsa mer om [ett Azure Container Service DC/OS-kluster med Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Om du har en Docker Swarm-lägesmiljö kan du läsa mer på Konfigurera en Log Analytics-agent för Docker Swarm.
     - Om du har ett Service Fabric-kluster kan du läsa mer på [Monitor-behållare med Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Läs [dockermotorn i Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) om du vill ha mer information om hur du installerar och konfigurerar Docker-motorer på datorer som kör Windows.

> [!IMPORTANT]
> Docker måste köras **innan** du installerar [Log Analytics-agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) på dina behållarvärdar. Om du redan har installerat agenten innan du installerar Docker måste du installera om Log Analytics-agenten för Linux. Mer information om Docker finns på [Dockers webbplats](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Installera och konfigurera Linux-behållarvärdar

När du har installerat Docker använder du följande inställningar för behållarens värd för att konfigurera agenten för användning med Docker. Först behöver du ditt Log Analytics-arbetsyte-ID och nyckel, som du hittar i Azure-portalen. På arbetsytan klickar du på > **Snabbstartsdatorer** för att visa ditt **arbetsyte-ID** och **primärnyckel**. **Quick Start**  Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

**För alla Linux-behållarvärdar utom CoreOS:**

- Mer information och anvisningar om hur du installerar Log Analytics-agenten för Linux finns i [Översikt över Logganalysagenten](../../azure-monitor/platform/log-analytics-agent.md).

**För alla Linux-behållarvärdar, inklusive CoreOS:**

Starta behållaren som du vill övervaka. Ändra och använd följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**För alla Azure Government Linux-behållare värdar inklusive CoreOS:**

Starta behållaren som du vill övervaka. Ändra och använd följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Byta från att använda en installerad Linux-agent till en i en behållare**

Om du tidigare har använt den direktinstallerade agenten och i stället vill använda en agent som körs i en behållare måste du först ta bort Log Analytics-agenten för Linux. Se [Avinstallera Log Analytics-agenten för Linux för](../../azure-monitor/learn/quick-collect-linux-computer.md) att förstå hur agenten avinstalleras.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurera en Log Analytics-agent för Docker Swarm

Du kan köra Log Analytics-agenten som en global tjänst på Docker Swarm. Använd följande information för att skapa en Log Analytics-agenttjänst. Du måste ange ditt Logganalysarbetsyte-ID och primärnyckel.

- Kör följande på huvudnoden.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Säkra hemligheter för Docker Swarm

För Docker Swarm använder du följande information när hemligheten för arbetsyte-ID och primärnyckel har skapats.

1. Kör följande på huvudnoden.

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

3. Kör följande kommando för att montera hemligheterna till den containeriserade Log Analytics-agenten.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurera en Log Analytics-agent för Red Hat OpenShift

Det finns tre sätt att lägga till Log Analytics-agenten i Red Hat OpenShift för att börja samla in containerövervakningsdata.

* [Installera Log Analytics-agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) direkt på varje OpenShift-nod  
* [Aktivera VM-tillägg för logganalys](../../azure-monitor/learn/quick-collect-azurevm.md) på varje OpenShift-nod som finns i Azure  
* Installera Log Analytics-agenten som en OpenShift-demonuppsättning  

I det här avsnittet täcker vi de steg som krävs för att installera Log Analytics-agenten som en OpenShift-demonuppsättning.  

1. Logga in på den Öppnashift-huvudnoden och kopiera [yaml-filen ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) från GitHub till huvudnoden och ändra värdet med logganalysarbetsyta-ID:t och med primärnyckeln.
2. Kör följande kommandon för att skapa ett projekt för Azure Monitor och ange användarkontot.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Om du vill distribuera demonuppsättningen kör du följande:

    `oc create -f ocp-omsagent.yaml`

4. Om du vill kontrollera att den är konfigurerad och fungerar korrekt skriver du följande:

    `oc describe daemonset omsagent`  

    och utgången bör likna:

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

Om du vill använda hemligheter för att skydda ditt LoggAnalys workspace-ID och primärnyckel när du använder Log Analytics-agentens daemon-set yaml-fil gör du följande steg.

1. Logga in på den Öppnashift-huvudnoden och kopiera [yaml-filen ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) och hemligt generera skript [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) från GitHub.  Det här skriptet genererar hemlighetspilret för Log Analytics Workspace ID och Primärnyckel för att skydda din utsöndrade information.  
2. Kör följande kommandon för att skapa ett projekt för Azure Monitor och ange användarkontot. Det hemliga generera skriptet frågar efter `<WSID>` ditt `<KEY>` Log Analytics Workspace ID och primärnyckel och när det är klart skapas filen ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Distribuera den hemliga filen genom att köra följande:

    `oc create -f ocp-secret.yaml`

4. Verifiera distributionen genom att köra följande:

    `oc describe secret omsagent-secret`  

    och utgången bör likna:  

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

5. Distribuera Yaml-filen Log Analytics-agent avdredd genom att köra följande:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verifiera distributionen genom att köra följande:

    `oc describe ds oms`

    och utgången bör likna:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurera en Log Analytics Linux-agent för Kubernetes

För Kubernetes använder du ett skript för att generera hemligheter yaml-filen för ditt arbetsyte-ID och primärnyckel för att installera Log Analytics-agenten för Linux. På log [analytics Docker Kubernetes GitHub-sidan](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) finns det filer som du kan använda med eller utan din hemliga information.

- Standardlogganalysagenten för Linux DaemonSet har ingen hemlig information (omsagent.yaml)
- Log Analytics-agenten för Linux DaemonSet yaml-filen använder hemlig information (omsagent-ds-secrets.yaml) med hemliga genereringsskript för att generera filen Secrets yaml (omsagentsecret.yaml).

Du kan välja att skapa omsagent DemonSets med eller utan hemligheter.

**Standard OMSagent DaemonSet yaml fil utan hemligheter**

- För standardfilen Log Analytics-agent DaemonSet `<WSID>` yaml ersätter du och `<KEY>` till ditt WSID och KEY. Kopiera filen till huvudnoden och kör följande:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standard OMSagent DaemonSet yaml fil med hemligheter**

1. Om du vill använda Log Analytics-agenten DaemonSet med hemlig information skapar du hemligheterna först.
    1. Kopiera skriptet och den hemliga mallfilen och se till att de finns i samma katalog.
        - Hemligt generera skript - secret-gen.sh
        - hemlig mall - secret-template.yaml
    2. Kör skriptet, till exempel följande exempel. Skriptet frågar efter Log Analytics Workspace ID och Primärnyckel och när du anger dem skapar skriptet en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Skapa hemligheter pod genom att köra följande:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Kontrollera genom att köra följande:

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

    5. Skapa din omsagent daemon-set genom att köra```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Kontrollera att Log Analytics-agenten DaemonSet körs, ungefär så här:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

För Kubernetes använder du ett skript för att generera hemligheterna yaml-filen för Arbetsyte-ID och Primärnyckel för Log Analytics-agenten för Linux. Använd följande exempelinformation med [omsagent yaml-filen](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) för att skydda din hemliga information.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurera en Log Analytics Windows-agent för Kubernetes

För Windows Kubernetes använder du ett skript för att generera hemligheterna yaml-filen för ditt arbetsyte-ID och primärnyckel för att installera Log Analytics-agenten. På log [analytics Docker Kubernetes GitHub-sidan](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) finns det filer som du kan använda med din hemliga information.  Du måste installera Log Analytics-agenten separat för huvud- och agentnoderna.  

1. Om du vill använda Log Analytics-agenten DaemonSet med hemlig information på huvudnoden loggar du in och skapar hemligheterna först.
    1. Kopiera skriptet och den hemliga mallfilen och se till att de finns i samma katalog.
        - Hemligt generera skript - secret-gen.sh
        - hemlig mall - secret-template.yaml

    2. Kör skriptet, till exempel följande exempel. Skriptet frågar efter Log Analytics Workspace ID och Primärnyckel och när du anger dem skapar skriptet en hemlig yaml-fil så att du kan köra den.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Skapa din omsagent daemon-set genom att köra```kubectl create -f omsagentsecret.yaml```
    4. Kör följande för att kontrollera:

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

    5. Skapa din omsagent daemon-set genom att köra```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Kontrollera att Log Analytics-agenten DaemonSet körs, ungefär så här:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Om du vill installera agenten på arbetarnoden, som kör Windows, följer du anvisningarna i avsnittet [installera och konfigurera Windows-behållarvärdar](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Använda Helm för att distribuera Log Analytics-agent på Linux Kubernetes

Om du vill använda rodret för att distribuera Log Analytics-agent i din Linux Kubernetes-miljö utför du följande steg.

1. Skapa din omsagent daemon-set genom att köra```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Resultaten kommer att se ut ungefär så här:

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

3. Du kan kontrollera status för omsagenten genom att köra: ```helm status "omsagent"``` och utdata kommer att se ut ungefär så här:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    För ytterligare information, besök [Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installera och konfigurera Windows-behållarvärdar

Använd informationen i avsnittet för att installera och konfigurera Windows-behållarvärdar.

#### <a name="preparation-before-installing-windows-agents"></a>Förberedelse innan du installerar Windows-agenter

Innan du installerar agenter på datorer som kör Windows måste du konfigurera Docker-tjänsten. Konfigurationen gör att Windows-agenten eller Azure Monitor-tillägget för virtuella datorer kan använda Docker TCP-socketen så att agenterna kan komma åt Docker-demonen på distans och samla in data för övervakning.

##### <a name="to-configure-the-docker-service"></a>Så här konfigurerar du Docker-tjänsten  

Utför följande PowerShell-kommandon för att aktivera TCP-pipe och namngivna pipe för Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Mer information om dockerdemonkonfigurationen som används med Windows Containers finns i [Docker Engine i Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Installera Windows-agenter

Om du vill aktivera övervakning av Windows- och Hyper-V-behållare installerar du MICROSOFT Monitoring Agent (MMA) på Windows-datorer som är behållarvärdar. Datorer som kör Windows i din lokala miljö finns i [Ansluta Windows-datorer till Azure Monitor](../../azure-monitor/platform/agent-windows.md). För virtuella datorer som körs i Azure ansluter du dem till Azure Monitor med [tillägget för den virtuella datorn](../../azure-monitor/learn/quick-collect-azurevm.md).

Du kan övervaka Windows-behållare som körs på Service Fabric. Det finns dock bara [virtuella datorer som körs i Azure](../../azure-monitor/learn/quick-collect-azurevm.md) och datorer som kör Windows i din lokala [miljö](../../azure-monitor/platform/agent-windows.md) för närvarande stöd för Service Fabric.

Du kan kontrollera att lösningen för behållarövervakning är korrekt inställd för Windows. Om du vill kontrollera om hanteringspaketet har hämtats korrekt letar du efter *ContainerManagement.xxx*. Filerna ska finnas i mappen C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.

## <a name="solution-components"></a>Lösningskomponenter

Från Azure-portalen navigerar du till *lösningsgalleriet* och lägger till **lösning för behållarövervakning**. Om du använder Windows-agenter installeras följande hanteringspaket på varje dator med en agent när du lägger till den här lösningen. Ingen konfiguration eller underhåll krävs för hanteringspaketet.

- *ContainerManagement.xxx* installerat i C:\Program\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Information om insamling av behållardata

Container Monitoring-lösningen samlar in olika prestandamått och loggar data från behållarvärdar och behållare med hjälp av agenter som du aktiverar.

Data samlas in var tredje minut av följande agenttyper.

- [Log Analytics-agent för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows-agent](../../azure-monitor/platform/agent-windows.md)
- [Logga vm-tillägg för logganalys](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Behållarposter

I följande tabell visas exempel på poster som samlats in av lösningen för behållarövervakning och de datatyper som visas i loggsökresultat.

| Datatyp | Datatyp i loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Dator, ObjectName, CounterName &#40;%Processortid, Diskläsning mb, diskskrivningar MB, Minnesanvändning MB, Nätverksmottagningsbyte, Nätverksskickbyte, Processoranvändning sek, Nätverksanvändning sek, Nätverks&#41;användning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning, Nätverksvärde, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Lager för behållare | `ContainerInventory` | TimeGenerated, Dator, behållarnamn, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventering av behållaravbildning | `ContainerImageInventory` | TimeGenerated, Dator, Image, ImageTag, ImageSize, VirtualSize, Running, Pauseed, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Behållare loggar | `ContainerLog` | TimeGenerated, Dator, bild-ID, behållarnamn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Tjänstlogg för behållare | `ContainerServiceLog`  | TimeGenerated, Dator, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Lager för containernod | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes inventering | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Behållare process | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes händelser | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Etiketter som läggs till *i PodLabel-datatyper* är dina egna anpassade etiketter. De bifogade PodLabel-etiketterna som visas i tabellen är exempel. Så, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` kommer att skilja sig åt i `PodLabel_yourlabel_s`din miljös datauppsättning och allmänt liknar .

## <a name="monitor-containers"></a>Övervaka containrar
När du har aktiverat lösningen i Azure-portalen visar panelen **Behållare** sammanfattande information om dina behållarvärdar och behållarna som körs i värdar.

![Behållare kakel](./media/containers/containers-title.png)

Panelen visar en översikt över hur många behållare du har i miljön och om de har misslyckats, körs eller stoppas.

### <a name="using-the-containers-dashboard"></a>Använda instrumentpanelen Behållare

Klicka på panelen **Behållare.** Därifrån ser du vyer ordnade efter:

- **Containerhändelser** - Visar behållarstatus och datorer med misslyckade behållare.
- **Container loggar** - Visar ett diagram över containerloggfiler som genereras över tiden och en lista över datorer med det högsta antalet loggfiler.
- **Kubernetes Events** - Visar ett diagram över Kubernetes-händelser som genererats över tid och en lista över orsakerna till att poddar genererade händelserna. *Den här datauppsättningen används endast i Linux-miljöer.*
- **Kubernetes namnområdeslager** - Visar antalet namnområden och poddar och visar deras hierarki. *Den här datauppsättningen används endast i Linux-miljöer.*
- **Lager för behållarnod** - Visar antalet orchestration-typer som används på behållarnoder/värdar. Datornoderna/värdarna visas också med antalet behållare. *Den här datauppsättningen används endast i Linux-miljöer.*
- **Inventering av behållarbilder** - Visar det totala antalet behållaravbildningar som används och antalet bildtyper. Antalet bilder visas också av bildtaggen.
- **Behållare Status** - Visar det totala antalet behållarnoder /värddatorer som har behållare som körs. Datorer visas också efter antalet värdar som körs.
- **Container process** - Visar ett linjediagram över behållarprocesser som körs över tiden. Behållare visas också genom att köra kommando/process i behållare. *Den här datauppsättningen används endast i Linux-miljöer.*
- **Container CPU Performance** - Visar ett linjediagram över den genomsnittliga CPU-användningen över tid för datornoder/värdar. Visar även datornoder/värdar baserat på genomsnittlig CPU-användning.
- **Prestanda för behållarminne** - Visar ett linjediagram över minnesanvändning över tid. Listar också datorminnesanvändning baserat på förekomstnamn.
- **Datorprestanda** – Visar linjediagram över procentandelen processorprestanda över tid, procent av minnesanvändningen över tid och megabyte ledigt diskutrymme över tid. Du kan hovra över valfri linje i ett diagram om du vill visa mer information.

Varje område på instrumentpanelen är en visuell representation av en sökning som körs på insamlade data.

![Instrumentpanelen Behållare](./media/containers/containers-dash01.png)

![Instrumentpanelen Behållare](./media/containers/containers-dash02.png)

Klicka på det övre området i området **Behållarstatus,** som visas nedan.

![Behållare status](./media/containers/containers-status.png)

Log Analytics öppnas och visar information om tillståndet för dina behållare.

![Logga Analytics för behållare](./media/containers/containers-log-search.png)

Härifrån kan du redigera sökfrågan för att ändra den för att hitta den specifika information du är intresserad av. Mer information om loggfrågor finns [i Loggfrågor i Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Felsöka genom att hitta en misslyckad behållare

Log Analytics markerar en behållare som **misslyckad** om den har avslutats med en utgångskod som inte är noll. Du kan se en översikt över fel och fel i miljön i området **Misslyckade behållare.**

### <a name="to-find-failed-containers"></a>Så här hittar du misslyckade behållare

1. Klicka på området **Behållarstatus.**  
   ![behållare status](./media/containers/containers-status.png)
2. Log Analytics öppnar och visar tillståndet för dina behållare, liknande följande.  
   ![behållare tillstånd](./media/containers/containers-log-search.png)
3. Expandera raden Misslyckades och klicka på + om du vill lägga till dess villkor i frågan. Kommentera sedan ut raden Summarize i frågan.
   ![misslyckade behållare](./media/containers/containers-state-failed-select.png)  
1. Kör frågan och expandera sedan en rad i resultatet för att visa bild-ID.  
   ![misslyckade behållare](./media/containers/containers-state-failed.png)  
1. Skriv följande i loggfrågan. `ContainerImageInventory | where ImageID == <ImageID>`om du vill se information om bilden, till exempel bildstorlek och antal stoppade och misslyckade bilder.  
   ![misslyckade behållare](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Frågeloggar för behållardata

När du felsöker ett specifikt fel kan det hjälpa dig att se var det förekommer i din miljö. Följande loggtyper hjälper dig att skapa frågor för att returnera den information du vill ha.

- **ContainerImageInventory** – Använd den här typen när du försöker hitta information ordnad efter bild och visa bildinformation som bild-ID eller storlekar.
- **ContainerInventory** – Använd den här typen när du vill ha information om behållarplatsen, vilka namn de har och vilka bilder de kör.
- **ContainerLog** – Använd den här typen när du vill hitta specifik fellogginformation och poster.
- **ContainerNodeInventory_CL**  Använd den här typen när du vill ha information om värd/nod där behållarna finns. Det ger dig Docker version, orkestrering typ, lagring och nätverksinformation.
- **ContainerProcess_CL** Använd den här typen för att snabbt se processen som körs i behållaren.
- **ContainerServiceLog** – Använd den här typen när du försöker hitta information om granskningsspårning för Docker-demonen, till exempel start-, stopp-, borttagnings- eller pull-kommandon.
- **KubeEvents_CL**  Använd den här typen om du vill visa kubernetes-händelserna.
- **KubePodInventory_CL**  Använd den här typen när du vill förstå klusterhierarkiinformationen.


### <a name="to-query-logs-for-container-data"></a>Så här frågar du efter loggloggar för behållardata

* Välj en bild som du vet har misslyckats nyligen och hitta felloggar för den. Börja med att hitta ett behållarnamn som kör avbildningen med en **ContainerInventory-sökning.** Sök till exempel efter`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Sök efter Ubuntu-behållare](./media/containers/search-ubuntu.png)

  Expandera valfri rad i resultatet om du vill visa information om den behållaren.

## <a name="example-log-queries"></a>Exempel på loggfrågor

Det är ofta användbart att skapa frågor som börjar med ett exempel eller två och sedan ändra dem så att de passar din miljö. Som utgångspunkt kan du experimentera med området **Exempelfrågor som** hjälper dig att skapa mer avancerade frågor.

![Behållare frågor](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Spara loggfrågor

Att spara frågor är en standardfunktion i Azure Monitor. Genom att spara dem har du de som du har hittat användbara till hands för framtida bruk.

När du har skapat en fråga som du tycker är användbar sparar du den genom att klicka på **Favoriter** högst upp på sidan Loggsökning. Sedan kan du enkelt komma åt den senare från sidan **Min instrumentpanel.**

## <a name="next-steps"></a>Nästa steg

[Frågeloggar](../log-query/log-query-overview.md) för att visa detaljerade behållardataposter.
