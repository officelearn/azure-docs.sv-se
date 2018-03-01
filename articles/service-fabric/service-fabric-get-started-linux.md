---
title: "Konfigurera en utvecklingsmiljö i Linux | Microsoft Docs"
description: "Installera runtime och SDK, och skapa ett lokalt utvecklingskluster i Linux. När du har slutfört den här installationen är du redo att börja bygga program."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 20239acba46996ed7ec84a2fbaf300190deebc7f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Förbereda utvecklingsmiljön i Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java- och .NET Core-utveckling.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="installation-methods"></a>Installationsmetoder

### <a name="1-script-installation"></a>1. Installation av skript

Ett skript anges för att underlätta installationen av Service Fabric runtime och Service Fabric common SDK tillsammans med **sfctl** CLI. Kör de manuella installationsstegen i nästa avsnitt för att avgöra vad som ska installeras och vilka licenser som godkänns. Vi förutsätter att du har godkänt licenserna för all programvara som installeras innan du kör skriptet. 

När skriptet har körts du kan hoppa direkt till [Konfigurera ett lokalt kluster](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Manuell installation
Följ resten av den här guiden för information om manuell installation av Service Fabric runtime och SDK.

## <a name="update-your-apt-sources"></a>Uppdatera dina APT-källor
Om du vill installera SDK och det tillhörande runtime-paketet via kommandoradsverktyget apt-get så måste du först uppdatera dina APT (Advanced Packaging Tool)-källor.

1. Öppna en terminal.
2. Lägga till Service Fabric-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Lägg till `dotnet`-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Lägg till den nya Gnu Privacy Guard-nyckeln (GnuPG eller GPG) i APT-nyckelringen.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Lägg till den officiella Docker GPG-nyckeln i din APT-nyckelring.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Ställ in Docker-databasen.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Uppdatera paketlistor baserat på nyligen tillagda lagringsplatser.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Installera och konfigurera Service Fabric SDK för lokal klusterkonfiguration

När du har uppdaterat källorna kan du installera SDK. Installera Service Fabric SDK-paketet, bekräfta installationen och acceptera licensavtalet (EULA).

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Följande kommandon automatiserar godkännandet av licensen för Service Fabric-paket:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Konfigurera ett lokalt kluster
  När installationen har slutförts ska du kunna starta ett lokalt kluster.

  1. Kör klusterinstallationsskriptet.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Öppna en webbläsare och gå till [Service Fabric Explorer](http://localhost:19080/Explorer). Om klustret har startats visas instrumentpanelen Service Fabric Explorer.

      ![Service Fabric Explorer på Linux][sfx-linux]

  Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästbehållare eller körbara gästprogram. Om du vill skapa nya tjänster med SDK:er för Java eller .NET Core, följer du installationsanvisningarna i följande avsnitt.


  > [!NOTE]
  > Fristående kluster stöds inte i Linux.
  >


>   [!TIP]
    Om du har en tillgänglig SSD-disk rekommenderas du att skicka en SSD-mapp med `--clusterdataroot` med devclustersetup.sh för överlägsna prestanda.

## <a name="set-up-the-service-fabric-cli"></a>Konfigurera Service Fabric CLI

[Service Fabric CLI](service-fabric-cli.md) innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program.
Installera CLI genom att följa anvisningarna i [Service Fabric CLI](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Konfigurera Yeoman-generatorer för behållare och körbara gästprogram
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa Service Fabric-program från en terminal med en Yeoman-mallgenerator. Följ dessa steg för att konfigurera Service Fabric Yeoman-mallgeneratorer:

1. Installera nodejs och NPM på datorn

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

2. Installera [Yeoman](http://yeoman.io/)-mallgeneratorn på datorn från NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installera Yeo-behållargeneratorn för Service Fabric och generatorn för körbara gästprogram från NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

När du har installerat generatorerna kan du skapa körbara gästprogram eller behållartjänster genom att köra `yo azuresfguest` respektive `yo azuresfcontainer`.

## <a name="set-up-net-core-20-development"></a>Konfigurera .NET Core 2.0 för utveckling

Installera [.NET Core 2.0 SDK för Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) om du vill börja [skapa Service Fabric-program i C#](service-fabric-create-your-first-linux-application-with-csharp.md). Paket för .NET Core 2.0 Service Fabric-program finns på NuGet.org (för närvarande som förhandsversion).

## <a name="set-up-java-development"></a>Konfigurera Java-utveckling

Installera JDK 1.8 och Gradle för att köra build-uppgifter om du vill skapa Service Fabric-tjänster som använder Java. Följande kodfragment installerar Open JDK 1.8 tillsammans med Gradle. Java-biblioteken för Service Fabric hämtas från Maven.

 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Installera Eclipse Neon-plugin-programmet (valfritt)

Du kan installera Eclipse-plugin-programmet för Service Fabric i Eclipse IDE för Java-utvecklare. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och behållarprogram utöver Service Fabric Java-program.

1. Kontrollera att du har den senaste Eclipse Neon-versionen och den senaste Buildship-versionen (1.0.17 eller senare) installerat. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Hjälp** > **Installationsinformation**. Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update].

2. Om du vill installera Service Fabric-plugin-programmet väljer du **Hjälp** > **Installera ny programvara**.

3. Ange **http://dl.microsoft.com/eclipse** i textrutan **Arbeta med**.

4. Klicka på **Lägg till**.

    ![Sidan Tillgänglig programvara][sf-eclipse-plugin]

5. Välj **Service Fabric**-plugin-programmet och klicka sedan på **Nästa**.

6. Slutför installationsstegen och acceptera licensavtalet för användare.

Om du redan har Service Fabric Eclipse-plugin-programmet installerat kontrollerar du att du har den senaste versionen. Du kan kontrollera detta genom att välja **Hjälp** > **Installationsinformation** och sedan söka efter Service Fabric i listan över installerade plugin-program. Välj **Uppdatera** om det finns en nyare version.

Mer information finns i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Uppdatera SDK och Runtime

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande kommandon:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Om du vill uppdatera Java SDK-binärfilerna från Maven måste du uppdatera versionsinformationen för motsvarande binärfil i ``build.gradle``-filen så att den pekar på den senaste versionen. Om du vill veta exakt var du behöver uppdatera versionen kan du titta i någon av ``build.gradle``-filerna i komma igång-exemplen för Service Fabric [här](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> När du uppdaterar paketen ovan kan ditt lokala miljökluster stoppas. Starta om ditt lokala kluster efter en uppgradering genom att följa instruktionerna på den här sidan.

## <a name="remove-the-sdk"></a>Ta bort SDK
Kör följande om du vill ta bort Service Fabric SDK:er:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>Nästa steg

* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)
* [Använd Service Fabric CLI för att hantera dina program](service-fabric-application-lifecycle-sfctl.md)
* [Skillnader mellan Service Fabric i Windows och Linux](service-fabric-linux-windows-differences.md)
* [Automatisera uppdatering av operativsystemet i Linux-klustret](service-fabric-patch-orchestration-application-linux.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
