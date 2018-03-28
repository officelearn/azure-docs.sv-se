---
title: Konfigurera en utvecklingsmiljö i Mac OS X så att den fungerar med Azure Service Fabric | Microsoft Docs
description: Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du är färdig med den här konfigurationen kommer du att kunna börja skapa appar i Mac OS X.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: bf0a03ace2f6b6e6b1c845785a452d0b75f35de8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurera din utvecklingsmiljö i Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Du kan skapa Azure Service Fabric-program som körs i Linux-kluster i Mac OS X. Det här dokumentet visar hur du konfigurerar din utvecklingsmiljö i Mac.

## <a name="prerequisites"></a>Nödvändiga komponenter
Azure Service Fabric kan inte köras internt i Mac OS X. För att du ska kunna köra ett lokalt Service Fabric-kluster tillhandahåller vi en förkonfigurerad Docker-behållaravbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne.
* Senaste versionen av [Docker](https://www.docker.com/).

>[!TIP]
>
>Följ anvisningarna i [Docker-dokumentationen](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) när du ska installera Docker på din Mac. [Verifiera installationen](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine) när du är färdig.
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Skapa en lokal behållare och konfigurera Service Fabric
Utför följande steg för att konfigurera en lokal Docker-behållare och köra ett Service Fabric-kluster på den:

1. Hämta Service Fabric onebox-behållaravbildningen från Docker-hubbens lagringsplats. Som standard hämtas avbildningen med den senaste Service Fabric-versionen. Läs mer om vissa revideringar på sidan [Docker-hubb](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    ```bash
    docker pull microsoft/service-fabric-onebox
    ```

2. Uppdatera konfigurationen av Docker-daemon på värden med följande inställningar och starta om Docker-daemon: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Du kan uppdatera inställningarna direkt i filen daemon.json som finns på Docker-installationssökvägen.
    
    >[!NOTE]
    >
    >Sökvägen för filen daemon.json kan variera mellan olika datorer. Den kan till exempel vara ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >Den rekommenderade metoden är att ändra konfigurationsinställningarna för daemon direkt i Docker. Välj **Docker-ikonen** och sedan **Inställningar** > **Daemon** > **Avancerat**.
    >

3. Starta en Service Fabric onebox-behållarinstans och använd avbildningen du hämtade i det första steget:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    >Ange ett namn för din behållarinstans så att du kan hantera den enklare. 
    >
    >Om programmet lyssnar på vissa portar måste du ange portarna med hjälp av ytterligare `-p`-taggar. Om programmet till exempel lyssnar på port 8080 lägger du till följande `-p`-tagg:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

4. Logga in på Docker-behållaren i interaktivt SSH-läge:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Kör konfigurationsskriptet som hämtar de nödvändiga beroendena och starta klustret på behållaren:

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. När du är färdig med steg 5 bläddrar du till `http://localhost:19080` från din Mac. Du bör se Service Fabric-utforskaren.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Konfigurera Service Fabric CLI (sfctl) på Mac

Följ anvisningarna på [Service Fabric CLI](service-fabric-cli.md#cli-mac) för att installera Service Fabric CLI (`sfctl`) på en Mac-dator.
Med CLI-kommandona kan du interagera med Service Fabric-entiteter som kluster, program och tjänster.

1. Om du vill ansluta till klustret innan du distribuerar programmen kör du kommandot nedan. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Skapa ditt program på Mac med Yeoman

Service Fabric har ramverktyg som hjälper dig att skapa ett Service Fabric-program från terminalen med en Yeoman-mallgenerator. Följ stegen nedan för att se till att Service Fabric Yeoman-mallgeneratorn fungerar i datorn:

1. Node.js och Node Package Manager (NPM) måste vara installerade på din Mac. Du kan installera programvaran med hjälp av [HomeBrew](https://brew.sh/) enligt följande:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installera [Yeoman](http://yeoman.io/)-mallgeneratorn på datorn från NPM:

    ```bash
    npm install -g yo
    ```
3. Installera den Yeoman-generator du vill använda enligt instruktionerna i [dokumentationen](service-fabric-get-started-linux.md) för att komma igång. Gör så här om du vill skapa Service Fabric-program med hjälp av Yeoman:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Om du vill skapa ett Service Fabric Java-program på Mac så måste du ha JDK 1.8 och Gradle installerade på värddatorn. Du kan installera programvaran med hjälp av [HomeBrew](https://brew.sh/) enligt följande: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Distribuera ditt program på Mac från terminalen

När du har skapat ett Service Fabric-program så kan du distribuera det med [CLI:t för Service Fabric](service-fabric-cli.md#cli-mac):

1. Anslut till Service Fabric-klustret som körs i behållarinstansen på din Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Gå till projektkatalogen och kör installationsskriptet:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Konfigurera .NET Core 2.0 för utveckling

Installera [.NET Core 2.0 SDK för Mac](https://www.microsoft.com/net/core#macos) om du vill börja [skapa Service Fabric-program i C#](service-fabric-create-your-first-linux-application-with-csharp.md). Paket för .NET Core 2.0 Service Fabric-program finns på NuGet.org, som för närvarande finns som förhandsversion.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Installera Service Fabric-plugin-programmet för Eclipse Neon på din Mac

Azure Service Fabric har ett plugin-program för Eclipse Neon för Java IDE. Med det här plugin-programmet blir det enklare att skapa och distribuera Java-tjänster. Gör [så här](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) om du vill installera eller uppdatera Service Fabric plugin-programmet för Eclipse till den senaste versionen. De andra stegen i [Service Fabric för Eclipse-dokumentationen](service-fabric-get-started-eclipse.md) är också relevanta: skapa ett program, lägga till en tjänst i ett program, avinstallera ett program och så vidare.

Det sista steget är att skapa en instans av behållaren med en sökväg som delas med värden. Den här typen av instansiering krävs för att plugin-programmet ska fungera med Docker-behållaren på din Mac. Till exempel:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Attributen har följande definitioner:
* `/Users/sayantan/work/workspaces/mySFWorkspace` är den fullständiga sökvägen till arbetsytan på din Mac.
* `/tmp/mySFWorkspace` är sökvägen inuti behållaren som arbetsytan ska mappas till.

>[!NOTE]
> 
>Om du har ett annat namn/en annan sökväg för din arbetsyta uppdaterar du de här värdena i `docker run`-kommandot.
> 
>Om du startar behållaren med ett annat namn än `sfonebox` så bör du uppdatera namnvärdet i filen testclient.sh i Java-programmet som är Service Fabric-aktör.
>

## <a name="next-steps"></a>Nästa steg
<!-- Links -->
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric Java-program i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
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
