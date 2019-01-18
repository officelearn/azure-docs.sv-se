---
title: Konfigurera Azure Service Fabric Linux-kluster på Windows | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in Service Fabric Linux-kluster som körs på datorer för utveckling av Windows. Detta är särskilt användbart för mellan plattformsutveckling.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 26acb3a2a0cefdca74d2c761ccddf89e18aa909a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388483"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurera ett Linux Service Fabric-kluster på din dator för Windows-utvecklare

Det här dokumentet beskriver hur du ställer in en lokal Linux Service Fabric i Windows development-datorer. Hur du konfigurerar ett lokalt kluster för Linux är användbar för att snabbt testa program riktade för Linux-kluster, men har utvecklats på en Windows-dator.

## <a name="prerequisites"></a>Förutsättningar
Linux-baserat Service Fabric-kluster inte köras internt i Windows. Om du vill köra ett lokalt Service Fabric-kluster, visas en förkonfigurerad Docker-behållaravbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Du kan följa stegen i den officiella Docker [dokumentation](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) att installera Docker på din Windows. 
> * När installationen är färdig verifierar du om den har installerats korrekt genom att följa anvisningarna [här](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal container och konfigurera Service Fabric
Om du vill konfigurera en lokal dockerbehållare har ett service fabric-kluster som körs på den och gör följande i PowerShell:


1. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Den rekommenderade metoden för att uppdatera är att gå till Docker-ikonen > Inställningar > Daemon > Avancerat, och uppdatera där. Starta sedan om Docker-daemon för att ändringarna ska börja gälla. 

2. Skapa din Service Fabric Image genom att skapa en fil med namnet `Dockerfile` i en ny katalog:

    ```dockerfile
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

6. När steg 5 är färdigt går du till ``http://localhost:19080`` från din Windows och du kommer att kunna se Service Fabric explorer. Du kan nu ansluta till det här klustret med hjälp av verktyg från någon från din dator för Windows-utvecklare och distribuera program för Linux Service Fabric-kluster. 

    > [!NOTE]
    > Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

7. När du är klar, stoppa och rensa behållaren med det här kommandot:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Kända begränsningar 
 
 Följande begränsningar är kända begränsningar i lokala kluster som körs i en container för Mac: 
 
 * DNS-tjänsten körs inte och stöds inte [Problem 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Nästa steg
* Kom igång med [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Se andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png