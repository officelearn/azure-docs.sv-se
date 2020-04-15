---
title: Konfigurera Linux-kluster för Azure Service Fabric i Windows
description: Den här artikeln beskriver hur du konfigurerar Linux-kluster för Service Fabric som körs på Windows-utvecklingsdatorer. Detta är särskilt användbart för plattformsoberoende utveckling.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 9f60b9d9b919a72250038ede2a2bd53278df79cb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309006"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurera ett Linux Service Fabric-kluster på din Windows-utvecklardator

Det här dokumentet beskriver hur du konfigurerar en lokal Linux-tjänst fabric på Windows-utvecklingsdatorer. Att konfigurera ett lokalt Linux-kluster är användbart för att snabbt testa program som är inriktade på Linux-kluster men utvecklas på en Windows-dator.

## <a name="prerequisites"></a>Krav
Linux-baserade Service Fabric-kluster körs inte internt i Windows. Om du vill köra ett lokalt Service Fabric-kluster tillhandahålls en förkonfigurerad Docker-behållaravbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker måste köras på Linux-läge

>[!TIP]
> * Du kan följa stegen som nämns i den officiella [Docker-dokumentationen](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) för att installera Docker i Din Windows. 
> * När installationen är färdig verifierar du om den har installerats korrekt genom att följa anvisningarna [här](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal container och konfigurera Service Fabric
Så här konfigurerar du en lokal Docker-behållare och har ett service fabric-kluster som körs på den:


1. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Det rekommenderade sättet att uppdatera är - gå till Docker Icon > Inställningar > Daemon > Advanced och uppdatera den där. Starta sedan om Docker-demonen för att ändringarna ska börja gälla. 

2. Skapa din Service Fabric Image genom att skapa en fil med namnet `Dockerfile` i en ny katalog:

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
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
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. För särskilda revideringar, besök [Docker Hub-sidan](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

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
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. Klustret tar en kort tid att starta, du kan visa loggar med följande kommando eller [http://localhost:19080](http://localhost:19080)hoppa till instrumentpanelen för att visa klustrens hälsa:

    ```powershell 
    docker logs sftestcluster
    ```

6. När steg 5 har slutförts kan ``http://localhost:19080`` du gå till från din Windows och du skulle kunna se Service Fabric explorer. Nu kan du ansluta till det här klustret med alla verktyg från din Windows-utvecklardator och distribuera program som är avsett för Linux Service Fabric-kluster. 

    > [!NOTE]
    > Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

7. När du är klar stoppar och rensar du behållaren med det här kommandot:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Kända begränsningar 
 
 Följande begränsningar är kända begränsningar i lokala kluster som körs i en container för Mac: 
 
 * DNS-tjänsten körs inte och stöds inte [Problem 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Nästa steg
* Komma igång med [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Kolla in andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
