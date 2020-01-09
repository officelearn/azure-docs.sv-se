---
title: Konfigurera Azure Service Fabric Linux-kluster i Windows
description: Den här artikeln beskriver hur du konfigurerar Service Fabric Linux-kluster som körs på Windows Development-datorer. Detta är särskilt användbart för plattforms oberoende utveckling.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462991"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurera ett Linux Service Fabric-kluster på Windows Developer-datorn

Det här dokumentet beskriver hur du konfigurerar en lokal Linux-Service Fabric på Windows Development-datorer. Det är praktiskt att konfigurera ett lokalt Linux-kluster för att snabbt testa program som är riktade mot Linux-kluster, men som utvecklas på en Windows-dator.

## <a name="prerequisites"></a>Krav
Linux-baserade Service Fabric kluster körs inte internt i Windows. För att köra ett lokalt Service Fabric-kluster tillhandahålls en förkonfigurerad Docker-behållar avbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker måste köras i Linux-läge

>[!TIP]
> * Du kan följa stegen i den officiella Docker- [dokumentationen](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) för att installera Docker på Windows. 
> * När installationen är färdig verifierar du om den har installerats korrekt genom att följa anvisningarna [här](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal container och konfigurera Service Fabric
Om du vill konfigurera en lokal Docker-behållare och ha ett Service Fabric-kluster som körs på den utför du följande steg i PowerShell:


1. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Det tillrådliga sättet att uppdatera är att gå till Docker-ikonen > Inställningar > daemon > Avancerat och uppdatera den där. Starta sedan om Docker daemon för att ändringarna ska börja gälla. 

2. Skapa din Service Fabric Image genom att skapa en fil med namnet `Dockerfile` i en ny katalog:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Du kan anpassa den här filen genom att lägga till fler program eller beroenden i din container.
    >Om du t.ex. lägger till `RUN apt-get install nodejs -y` tillåter du stöd för `nodejs`-program som körbara gästfiler.
    
    >[!TIP]
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. Läs mer om vissa revideringar på sidan [Docker-hubb](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Om du vill skapa din återanvändbara avbildning från `Dockerfile`, så öppna en terminal och `cd` som direkt håller din `Dockerfile` och kör:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Den här åtgärden kan ta lite tid, men det behöver bara göras en gång.

4. Nu kan du snabbt starta en lokal kopia av Service Fabric, närhelst du så behöver, genom att köra:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ange ett namn för din containerinstans så att du kan hantera den enklare. 
    >
    >Om programmet lyssnar på vissa portar måste du ange portarna med hjälp av ytterligare `-p`-taggar. Om programmet till exempel lyssnar på port 8080 lägger du till följande `-p`-tagg:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Det tar en liten stund för klustret att starta. Du kan se loggar med följande kommando eller gå till instrumentpanelen och visa klusterhälsotillståndet [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. När steg 5 har slutförts kan du gå till ``http://localhost:19080`` från Windows och du kan se Service Fabric Explorer. Nu kan du ansluta till det här klustret med valfria verktyg från Windows Developer-datorn och distribuera program som är riktade till Linux Service Fabric-kluster. 

    > [!NOTE]
    > Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

7. När du är färdig stoppar du och rensar behållaren med det här kommandot:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Kända begränsningar 
 
 Följande begränsningar är kända begränsningar i lokala kluster som körs i en container för Mac: 
 
 * DNS-tjänsten körs inte och stöds inte [Problem 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Nästa steg
* Kom igång med [Sol förmörkelse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Kolla ut andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
