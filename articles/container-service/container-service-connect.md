---
title: Ansluta till ett Azure Container Service-kluster | Microsoft Docs
description: "Ansluta till ett Kubernetes-, DC/OS- eller Docker Swarm-kluster i Azure Container Service från en fjärrdator"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3dfa2c56dd6d3e0fe7757995d284cebe172eabc4
ms.lasthandoff: 04/03/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Skapa en fjärranslutning till ett Kubernetes-, DC/OS- eller Docker Swarm-kluster
När du har skapat ett Azure Container Service-kluster måste du ansluta till klustret för att distribuera och hantera arbetsbelastningar. Den här artikeln beskriver hur du ansluter till den virtuella huvuddatorn i klustret från en fjärrdator. 

Kubernetes-, DC/OS- och Docker Swarm-klustren tillhandahåller HTTP-slutpunkter lokalt. För Kubernetes exponeras den här slutpunkten på ett säkert sätt på Internet och du kan komma åt den genom att köra `kubectl`-kommandoradsverktyget från valfri Internetansluten dator. 

För DC/OS och Docker Swarm måste du skapa en SSH-tunnel (Secure Shell) till ett internt system. När tunneln har upprättats kan du köra kommandon som använder HTTP-slutpunkter och visa klustrets webbgränssnitt från det lokala systemet. 


## <a name="prerequisites"></a>Krav

* Ett Kubernetes-, DC/OS- eller Swarm-kluster [som distribuerats i Azure Container Service](container-service-deployment.md).
* En SSH RSA-fil för privat nyckel som motsvarar den offentliga nyckel som lades till i klustret under distributionen. Dessa kommandon förutsätter att den privata SSH-nyckeln finns i `$HOME/.ssh/id_rsa` på din dator. Mer information finns i dessa anvisningar för [OS X och Linux](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../virtual-machines/linux/ssh-from-windows.md). Om SSH-anslutningen inte fungerar kan du behöva [återställa dina SSH-nycklar](../virtual-machines/linux/troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Ansluta till ett Kubernetes-kluster

Följ stegen nedan för att installera och konfigurera `kubectl` på datorn.

> [!NOTE] 
> På Linux eller OS X kan du behöva köra kommandona i det här avsnittet med `sudo`.
> 

### <a name="install-kubectl"></a>Installera kubectl
Ett sätt att installera det här verktyget är att använda kommandot `az acs kubernetes install-cli` från Azure CLI 2.0. Om du vill köra det här kommandot kontrollerar du att den senaste versionen av Azure CLI 2.0 är [installerad](/cli/azure/install-az-cli2) och att du är inloggad på ett Azure-konto (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Du kan också hämta den senaste klienten direkt på [Kubernetes-sidan med versioner](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Mer information finns i [Installera och konfigurera kubectl](https://kubernetes.io/docs/user-guide/prereqs/).

### <a name="download-cluster-credentials"></a>Hämta autentiseringsuppgifter för kluster
När du har installerat `kubectl` måste du kopiera klustrets autentiseringsuppgifter till datorn. Ett sätt att hämta autentiseringsuppgifterna är att använda kommandot `az acs kubernetes get-credentials`. Ange namnet på resursgruppen och namnet på behållartjänstens resurs:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Det här kommandot hämtar autentiseringsuppgifterna för klustret till `$HOME/.kube/config`, där `kubectl` förväntar sig att hitta dem.

Du kan också använda `scp` för att kopiera filen från `$HOME/.kube/config` på den virtuella huvuddatorn till din dator på ett säkert sätt. Exempel:

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Om du använder Windows måste du använda Bash i Ubuntu för Windows, PuTTy-klienten för säker filkopiering eller ett liknande verktyg.



### <a name="use-kubectl"></a>Använda kubectl

När du har konfigurerat `kubectl` kan du testa anslutningen genom att visa en lista över noderna i klustret:

```console
kubectl get nodes
```

Du kan prova andra `kubectl`-kommandon. Du kan till exempel visa instrumentpanelen för Kubernetes. Börja med att köra en proxy till Kubernetes API-servern:

```console
kubectl proxy
```

Nu är användargränssnittet för Kubernetes tillgängligt på: `http://localhost:8001/ui`.

Mer information finns i [snabbstarten för Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Ansluta till ett DC/OS- eller Swarm-kluster

Om du vill använda DC/OS- och Docker Swarm-kluster som distribueras av Azure Container Service, följer du dessa instruktioner för att skapa en SSH-tunnel (Secure Shell) från ditt lokala Linux-, OS X- eller Windows-system. 

> [!NOTE]
> Dessa anvisningar fokuserar på tunnling av TCP-trafik över SSH. Du kan också starta en interaktiv SSH-session med något av de interna klusterhanteringssystemen, men det rekommenderas inte. Det finns en risk för oavsiktliga konfigurationsändringar om du arbetar direkt i ett internt system.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Skapa en SSH-tunnel i Linux eller OS X
Det första du ska göra när du ska skapa en SSH-tunnel i Linux eller OS X är att leta upp det offentliga DNS-namnet på belastningsutjämnade huvudservrar. Följ de här stegen:


1. På [Azure Portal](https://portal.azure.com) bläddrar du till resursgruppen som innehåller behållartjänstens kluster. Expandera resursgruppen så att varje resurs visas. 

2. Klicka på resursen för behållartjänsten och klicka sedan på **Översikt**. **Master-FQDN** för klustret visas under **Essentials**. Spara det här namnet för senare användning. 

    ![Offentligt DNS-namn](media/pubdns.png)

    Du kan också köra `az acs show`-kommandot i behållartjänsten. Leta reda på egenskapen **Master Profile:fqdn** i utdata från kommandot.

3. Öppna ett gränssnitt och kör `ssh`-kommandot genom att ange följande värden: 

    **LOCAL_PORT** är TCP-porten som ska anslutas till på serversidan av tunneln. För Swarm anger du port 2375. För DC/OS anger du 80.  
    **REMOTE_PORT** är porten för den slutpunkt som du vill exponera. Använd port 2375 för Swarm. Använd port 80 för DC/OS.  
    **USERNAME** är det användarnamn som angavs när du distribuerade klustret.  
    **DNSPREFIX** är det DNS-prefix som du angav när du distribuerade klustret.  
    **REGION** är den region där resursgruppen finns.  
    **PATH_TO_PRIVATE_KEY** [VALFRITT] är sökvägen till den privata nyckeln som motsvarar den offentliga nyckeln som du angav när du skapade klustret. Använd det här alternativet med flaggan `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > SSH-anslutningsporten är 2200 och inte standardporten 22. I ett kluster med flera virtuella huvuddatorer är detta anslutningsporten till den första virtuella huvuddatorn.
    > 



Se exemplen för DC/OS och Swarm i följande avsnitt.    

### <a name="dcos-tunnel"></a>DC/OS-tunnel
Om du vill öppna en tunnel för DC/OS-slutpunkter kör du ett kommando på följande sätt:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Du kan ange en annan lokal port än port 80, till exempel port 8888. Men vissa länkar i webbgränssnitt kanske inte fungerar när du använder den här porten.

Nu kan du komma åt DC/OS-slutpunkter från den lokala datorn via följande URL:er (förutsatt att port 80 används som lokal port):

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

På samma sätt kan du nå REST API:erna för alla program via den här tunneln.

### <a name="swarm-tunnel"></a>Swarm-tunnel
Om du vill öppna en tunnel till Swarm-slutpunkten kör du ett kommando liknande följande:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

Du kan nu ange din DOCKER_HOST-miljövariabel på följande sätt. Du kan fortsätta att använda ditt Docker-kommandoradsgränssnitt (CLI) som vanligt.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Skapa en SSH-tunnel i Windows
Det finns flera olika sätt att skapa SSH-tunnlar i Windows. Det här avsnittet beskriver hur du använder PuTTY för att skapa tunneln.

1. [Ladda ned PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) till din Windows-dator.

2. Kör appen.

3. Ange ett värdnamn som består av klustrets adminanvändarnamn och det offentliga DNS-namnet på den första huvudservern i klustret. **Värdnamnet** liknar `adminuser@PublicDNSName`. Ange 2200 som **Port**.

    ![PuTTY-konfiguration 1](media/putty1.png)

4. Välj **SSH > Aut**. Lägg till en sökväg till filen för privat nyckel (PPK-format) för autentisering. Du kan använda ett verktyg som [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) för att skapa den här filen från SSH-nyckeln som används för att skapa klustret.

    ![PuTTY-konfiguration 2](media/putty2.png)

5. Välj **SSH > Tunnlar** och konfigurera följande vidarebefordrade portar:

    * **Källport:** Använd 80 för DC/OS eller 2375 för Swarm.
    * **Mål:** Använd localhost:80 för DC/OS eller localhost:2375 för Swarm.

    Exemplen nedan har konfigurerats för DC/OS, men det ser ungefär likadant ut för Docker Swarm.

    > [!NOTE]
    > Port 80 får inte användas för något annat när du skapar den här tunneln.
    > 

    ![PuTTY-konfiguration 3](media/putty3.png)

6. När du är klar klickar du på **Session > Spara** för att spara anslutningskonfigurationen.

7. Anslut till PuTTY-sessionen genom att klicka på **Öppna**. När du ansluter kan du se portkonfigurationen i PuTTY-händelseloggen.

    ![PuTTY-händelselogg](media/putty4.png)

När du har konfigurerat tunneln för DC/OS kan du komma åt de relaterade slutpunkterna på:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

När du har konfigurerat tunneln för Docker Swarm öppnar du Windows-inställningarna och konfigurerar en systemmiljövariabel med namnet `DOCKER_HOST` och värdet `:2375`. Därefter kan du använda Swarm-klustret via Docker CLI.

## <a name="next-steps"></a>Nästa steg
Distribuera och hantera behållare i klustret:

* [Arbeta med Azure Container Service och Kubernetes](container-service-kubernetes-ui.md)
* [Arbeta med Azure Container Service och DC/OS](container-service-mesos-marathon-rest.md)
* [Arbeta med Azure Container Service och Docker Swarm](container-service-docker-swarm.md)


