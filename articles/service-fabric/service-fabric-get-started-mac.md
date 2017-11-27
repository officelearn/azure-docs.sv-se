---
title: "Konfigurera en utvecklingsmiljö i Mac OS X så att den fungerar med Azure Service Fabric | Microsoft Docs"
description: "Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurera din utvecklingsmiljö i Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Du kan skapa Service Fabric-program som körs i Linux-kluster i Mac OS X. Det här dokumentet visar hur du konfigurerar din utvecklingsmiljö i Mac.

## <a name="prerequisites"></a>Krav
Service Fabric kan inte köras internt i OS X. För att du ska kunna köra ett lokalt Service Fabric-kluster tillhandahåller vi en förkonfigurerad Docker-behållaravbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker](https://www.docker.com/)
* Åtkomst till Service Fabric One-box Docker-[behållaravbildning](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * Du kan följa anvisningarna i den officiella Docker-[dokumentationen](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) för att installera Docker på Mac. 
> * När installationen är färdig verifierar du om den har installerats korrekt genom att följa anvisningarna [här](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal behållare och konfigurera Service Fabric
Utför följande steg för att konfigurera en lokal Docker-behållare och för att köra ett Service Fabric-kluster på den:

1. Hämta avbildningen från Docker-hubblagringsplatsen:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Du kan uppdatera detta på daemon.json direkt via Docker-installationssökvägen (platsen kan variera mellan olika datorer, till exempel ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json). Den rekommenderade metoden för att uppdatera är att gå till Docker-ikonen > Inställningar > Daemon > Avancerat, och uppdatera där.

3. Starta en behållarinstans för Service Fabric One-box med avbildningen:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Genom att ange ett namn för din behållarinstans kan du hantera den enklare. 
    > * Om programmet lyssnar på vissa portar måste det anges med hjälp av ytterligare -p-taggar. Om programmet till exempel lyssnar på port 8080 kör du docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Logga in på Docker-behållaren i interaktivt SSH-läge:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Kör konfigurationsskriptet som ska hämta de nödvändiga beroendena och starta därefter klustret på behållaren.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. När steg 5 är färdigt går du till ``http://localhost:19080`` från Mac-datorn så kan du se Service Fabric Explorer.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Konfigurera Service Fabric CLI (sfctl) på Mac

Följ anvisningarna på [Service Fabric CLI](service-fabric-cli.md#cli-mac) för att installera Service Fabric CLI (`sfctl`) på en Mac-dator.
CLI-kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster, program och tjänster.

## <a name="create-application-on-your-mac-using-yeoman"></a>Skapa program på Mac med Yeoman

Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa ett Service Fabric-program från terminalen med en Yeoman-mallgenerator. Följ stegen nedan för att se till att du har Service Fabric Yeoman-mallgeneratorn på datorn.

1. Du måste ha Node.js och NPM installerade på din Mac. Annars kan du installera Node.js och NPM med Homebrew genom att följa de här stegen:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installera [Yeoman](http://yeoman.io/)-mallgeneratorn på datorn från NPM.

    ```bash
    npm install -g yo
    ```
3. Installera den Yeoman-generator som du vill använda enligt instruktionerna i [dokumentationen](service-fabric-get-started-linux.md) för att komma igång. Om du vill skapa Service Fabric-program med hjälp av Yeoman följer du stegen:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Om du vill skapa ett Service Fabric Java-program på Mac så måste du ha JDK 1.8 och Gradle installerade på värddatorn. Om du inte redan har gjort det kan du installera det med hjälp av [HomeBrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Distribuera program på Mac från terminalen

När du har skapat ett Service Fabric-program så kan du distribuera det med [Service Fabric CLI](service-fabric-cli.md#cli-mac) genom att följa anvisningarna:

1. Anslut till ett Service Fabric-kluster som körs i behållarinstansen på din Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Gå till projektkatalogen och kör installationsskriptet.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Konfigurera .NET Core 2.0 för utveckling

Installera [.NET Core 2.0 SDK för Mac](https://www.microsoft.com/net/core#macos) om du vill börja [skapa Service Fabric-program i C#](service-fabric-create-your-first-linux-application-with-csharp.md). Paket för .NET Core 2.0 Service Fabric-program finns på NuGet.org (för närvarande som förhandsversion).

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Installera Service Fabric-plugin-programmet för Eclipse Neon på din Mac

Service Fabric innehåller ett plugin-program för **Eclipse Neon för Java IDE** som förenklar processen med att skapa, bygga och distribuera Java-tjänster. Du kan följa installationsstegen i den här allmänna [dokumentationen](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) som beskriver hur du installerar eller uppdaterar Service Fabric Eclipse-plugin-programmet till den senaste versionen.

Alla andra steg som anges i [Service Fabric Eclipse-dokumentationen](service-fabric-get-started-eclipse.md) för att skapa ett program, till exempel att lägga till en tjänst till programmet, installera eller avinstallera programmet osv. gäller även här.

Om du vill att plugin-programmet för Service Fabric Eclipse ska fungera med Docker-behållaren på Mac bör du förutom att följa ovanstående steg även instantiera behållaren med en sökväg som delas med värden, enligt följande:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
där ``/Users/sayantan/work/workspaces/mySFWorkspace`` är den absoluta sökvägen för arbetsytan på Mac och ``/tmp/mySFWorkspace`` är sökvägen i behållaren dit den mappas.

> [!NOTE]
>1. Om din arbetsyta/sökväg är annorlunda bör du uppdatera den/dem enligt kommandot ``docker run`` ovan.
>2. Om du startar behållaren med ett annat namn än ``sfonebox`` så bör du uppdatera till samma i filen ``testclient.sh`` i Java-programmet som är Service Fabric-aktör.

## <a name="next-steps"></a>Nästa steg
<!-- Links -->
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ett Service Fabric-kluster i Azure-portalen](service-fabric-cluster-creation-via-portal.md)
* [Skapa ett Service Fabric-kluster med Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)
* [Använd Service Fabric CLI för att hantera dina program](service-fabric-application-lifecycle-sfctl.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship