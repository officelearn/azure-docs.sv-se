---
title: Konfigurera en utvecklingsmiljö i Linux | Microsoft Docs
description: Installera runtime och SDK, och skapa ett lokalt utvecklingskluster i Linux. När den här installationen är klar kan du börja bygga program.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 6609239cb859cb39f72fbdd7f76609b5dc8e1eca
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Förbereda utvecklingsmiljön i Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java- och .NET Core-utveckling. 

Stegen i den här artikeln förutsätter att du installerar internt på Linux eller att du använder Service Fabric OneBox-behållaravbildningen, `microsoft/service-fabric-onebox`.

Det stöds inte att installera Service Fabric runtime och SDK på Windows-undersystem för Linux. Du kan hantera Service Fabric-entiteter som finns någon annanstans i molnet eller i den lokala infrastrukturen med kommandoradsgränssnittet (CLI) för Azure Service Fabric, som stöds. Information om hur du installerar kommandoradsgränssnittet finns i [Konfigurera Service Fabric CLI](./service-fabric-cli.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (`Xenial Xerus`)

    Kontrollera att `apt-transport-https`-paketet har installerats.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)


## <a name="installation-methods"></a>Installationsmetoder

### <a name="script-installation-ubuntu"></a>Skriptinstallation (Ubuntu)

Ett skript finns tillgängligt som underlättar installationen av Service Fabric Runtime och Service Fabric SDK med **sfctl**-kommandoradsgränssnittet. Stegen för manuell installation finns i nästa avsnitt. Du ser vad som installeras och de tillhörande licenserna. Vi förutsätter att du har godkänt licenserna för all programvara som installeras innan du kör skriptet.

När skriptet har körts kan du gå vidare till [Konfigurera ett lokalt kluster](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>Manuell installation
Om du vill installera Service Fabric Runtime och SDK manuellt följer du stegen i resten av den här guiden.

## <a name="update-your-apt-sources-or-yum-repositories"></a>Uppdatera dina APT-källor eller Yum-lagringsplatser
Om du vill installera SDK och det tillhörande runtime-paketet via kommandoradsverktyget apt-get så måste du först uppdatera dina APT-källor (Advanced Packaging Tool).

### <a name="ubuntu"></a>Ubuntu

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


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

1. Öppna en terminal.
2. Ladda ned och installera fler paket för Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Lägg till EfficiOS RHEL7-paketdatabasen i systemet.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importera EfficiOS-paketets signeringsnyckel till den lokala GPG-nyckelringen.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Lägg till Microsoft RHEL-lagringsplatsen i systemet.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. Installera .NET SDK.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Installera och konfigurera Service Fabric SDK för ett lokalt kluster

När du har uppdaterat källorna kan du installera SDK. Installera Service Fabric SDK-paketet, bekräfta installationen och acceptera licensavtalet.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Följande kommandon automatiserar godkännandet av licensen för Service Fabric-paket:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

```bash
sudo yum install servicefabricsdkcommon
```

Service Fabric Runtime som medföljer SDK-installationen innehåller paketen i följande tabell. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | OpenJDK 1.8 | Implicit från npm | senaste |
RHEL | - | OpenJDK 1.8 | Implicit från npm | senaste |

## <a name="set-up-a-local-cluster"></a>Konfigurera ett lokalt kluster
Starta ett lokalt kluster när installationen är klar.

1. Kör klusterinstallationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Öppna en webbläsare och gå till [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). När klustret startas visas Service Fabric Explorer-instrumentpanelen. Konfigurationen av klustret kan ta flera minuter. Om din webbläsare inte kan öppna URL:en, eller om Service Fabric Explorer inte visar att systemet är klart, väntar du några minuter och försöker sedan igen.

    ![Service Fabric Explorer på Linux][sfx-linux]

    Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästbehållare eller körbara gästprogram. Om du vill skapa nya tjänster med SDK:er för Java eller .NET Core, följer du installationsanvisningarna i följande avsnitt.


> [!NOTE]
> Fristående kluster stöds inte i Linux.


> [!TIP]
> Om du har en tillgänglig SSD-disk rekommenderar vi att du skickar en SSD-mappsökväg med hjälp av `--clusterdataroot` med devclustersetup.sh för överlägsna prestanda.

## <a name="set-up-the-service-fabric-cli"></a>Konfigurera Service Fabric CLI

[Service Fabric CLI](service-fabric-cli.md) innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Installera CLI genom att följa anvisningarna i [Service Fabric CLI](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Konfigurera Yeoman-generatorer för behållare och körbara gästprogram
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa Service Fabric-program från en terminal med en Yeoman-mallgenerator. Följ dessa steg för att konfigurera Service Fabric Yeoman-mallgeneratorer:

1. Installera Node.js och npm på datorn.

    * Ubuntu
        ```bash
        sudo apt-get install npm
        sudo apt install nodejs-legacy
        ```

    * Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)
        ```bash
        sudo yum install nodejs
        sudo yum install npm
        ```
2. Installera [Yeoman](http://yeoman.io/)-mallgeneratorn från npm på datorn.

    ```bash
    sudo npm install -g yo
    ```
3. Installera Yeo-behållargeneratorn för Service Fabric och generatorn för körbara gästprogram från npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

När du har installerat generatorerna skapar du körbara gästprogram eller behållartjänster genom att köra `yo azuresfguest` respektive `yo azuresfcontainer`.

## <a name="set-up-net-core-20-development"></a>Konfigurera .NET Core 2.0 för utveckling

Installera [.NET Core 2.0 SDK för Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) om du vill börja [skapa Service Fabric-program i C#](service-fabric-create-your-first-linux-application-with-csharp.md). NuGet.org tillhandahåller paket för Service Fabric-program med .NET Core 2.0, som för närvarande finns som en förhandsversion.

## <a name="set-up-java-development"></a>Konfigurera Java-utveckling

Installera JDK 1.8 och Gradle för att köra build-uppgifter om du vill skapa Service Fabric-tjänster som använder Java. Följande kodfragment installerar Open JDK 1.8 tillsammans med Gradle. Java-biblioteken för Service Fabric hämtas från Maven.


* Ubuntu

    ```bash
    sudo apt-get install openjdk-8-jdk-headless
    sudo apt-get install gradle
    ```

* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Du måste också installera Service Fabric Yeo-generatorn för körbara Java-filer. Kontrollera att [Yeoman är installerat](#set-up-yeoman-generators-for-containers-and-guest-executables) och kör sedan följande kommando:

  ```bash
  sudo npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Installera plugin-programmet Eclipse (valfritt)

Du kan installera Eclipse-pluginprogrammet för Service Fabric i Eclipse IDE för Java-utvecklare eller Java EE-utvecklare. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och behållarprogram utöver Service Fabric Java-program.

> [!IMPORTANT]
> Service Fabric plugin-programmet kräver Eclipse Neon eller en senare version. Efter den här anteckningen följer instruktioner om hur du kontrollerar din Eclipse-version. Om du har en tidigare version av Eclipse installerad kan du hämta nyare versioner från [Eclipse-webbplatsen](https://www.eclipse.org). Vi rekommenderar att du inte installerar ovanpå (skriver över) en befintlig installation av Eclipse. Ta bort den gamla versionen innan du kör installationsprogrammet eller installera den nya versionen i en annan katalog.
> 
> I Ubuntu rekommenderar vi att du installerar direkt från Eclipses webbplats i stället för att använda ett installationspaket (`apt` eller `apt-get`). På så sätt kan du vara säker på att du får den senaste versionen av Eclipse. Du kan installera Eclipse IDE för Java-utvecklare eller för Java EE-utvecklare.

1. I Eclipse kontrollerar du att Eclipse Neon eller senare och Buildship version 2.2.1 eller senare är installerade. Kontrollera vilka versioner de installerade komponenterna har genom att välja **Hjälp** > **Om Eclipse** > **Installationsinformation**. Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update].

2. Om du vill installera Service Fabric-plugin-programmet väljer du **Hjälp** > **Installera ny programvara**.

3. I rutan **Work with** (Arbeta med) skriver du **http://dl.microsoft.com/eclipse**.

4. Välj **Lägg till**.

    ![Sidan Tillgänglig programvara][sf-eclipse-plugin]

5. Välj **ServiceFabric**-plugin-programmet och välj sedan **Nästa**.

6. Utför följande installationssteg. Acceptera sedan licensavtalet.

Om du redan har Service Fabric Eclipse-plugin-programmet installerat kontrollerar du att du har den senaste versionen. Det kontrollerar du genom att välja **Hjälp** > **Om Eclipse** > **Installationsinformation**. Sök sedan efter Service Fabric i listan över installerade plugin-program. Välj **Uppdatera** om det finns en nyare version.

Mer information finns i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Uppdatera SDK och Runtime

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande kommandon.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Om du vill uppdatera Java SDK-binärfilerna från Maven måste du uppdatera versionsinformationen för motsvarande binärfil i ``build.gradle``-filen så att den pekar på den senaste versionen. Om du vill veta exakt var du ska uppdatera versionen kan du titta i någon av ``build.gradle``-filerna i [komma igång-exemplen för Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> När du uppdaterar paketen ovan kan ditt lokala miljökluster stoppas. Starta om ditt lokala kluster efter en uppgradering genom att följa instruktionerna i den här artikeln.

## <a name="remove-the-sdk"></a>Ta bort SDK
Kör följande kommandon om du vill ta bort Service Fabric SDK:erna.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    sudo npm uninstall generator-azuresfcontainer
    sudo npm uninstall generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

    ```bash
    sudo yum remote servicefabric servicefabicsdkcommon
    sudo npm uninstall generator-azuresfcontainer
    sudo npm uninstall generator-azuresfguest
    ```

## <a name="next-steps"></a>Nästa steg

* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)
* [Hantera program med hjälp av Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
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
