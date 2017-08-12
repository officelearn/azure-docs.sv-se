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
ms.date: 8/10/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: da6a8b4824d7215eb1db131680856ac04003f5aa
ms.contentlocale: sv-se
ms.lasthandoff: 07/13/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>Förbereda utvecklingsmiljön i Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java och .NET Core.

## <a name="prerequisites"></a>Krav

Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Uppdatera dina APT-källor
Om du vill installera SDK och det tillhörande runtime-paketet via kommandoradsverktyget apt-get så måste du först uppdatera dina APT (Advanced Packaging Tool)-källor.

1. Öppna en terminal.
2. Lägga till Service Fabric-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
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

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Installera och konfigurera SDK för behållare och körbara gästprogram

När du har uppdaterat källorna kan du installera SDK.

1. Installera Service Fabric SDK-paketet, bekräfta installationen och acceptera licensavtalet (EULA).

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Följande kommandon automatiserar godkännandet av licensen för Service Fabric-paket:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   ```
    
2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

När du har installerat det vanliga SDK-paketet kan du skapa appar med körbara gästprogram eller behållartjänster genom att köra `yo azuresfguest` eller `yo azuresfcontainer`. Du kan behöva ange miljövariabeln $NODE_PATH till den plats där nodmodulerna finns. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Om du använder miljön som rot kan du behöva ange variabeln med följande kommando:

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Du kanske vill lägga till de här kommandona i filen ~/.bashrc så att du inte behöver ange miljövariabeln vid varje inloggning.
>

## <a name="set-up-the-xplat-service-fabric-cli"></a>Konfigurera XPlat Service Fabric CLI
[XPlat CLI][azure-xplat-cli-github] innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Det är baserat på Node.js. Det innebär att du måste [kontrollera att du har installerat Node][install-node] innan du fortsätter med följande instruktioner:

1. Klona GitHub-repon till din utvecklingsdator.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Växla till den klonade repon och installera CLI-beroendena med nodpaketshanteraren (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Skapa en symlink från mappen `bin/azure` för den klonade lagringsplatsen till `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Slutligen aktiverar du Service Fabric-kommandon för automatisk komplettering.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

### <a name="set-up-azure-cli-20"></a>Konfigurera Azure CLI 2.0

Som ett alternativ till XPlat CLI finns nu en Service Fabric-kommandomodul som ingår i Azure CLI.

Mer information om att installera Azure CLI 2.0 och att använda Service Fabric-kommandon finns i [Kom igång med Service Fabric och Azure CLI 2.0](service-fabric-azure-cli-2-0.md).

## <a name="set-up-a-local-cluster"></a>Konfigurera ett lokalt kluster
Om installationen har slutförts kan du starta ett lokalt kluster.

1. Kör klusterinstallationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Öppna en webbläsare och gå till [Service Fabric Explorer](http://localhost:19080/Explorer). Om klustret har startats visas instrumentpanelen Service Fabric Explorer.

    ![Service Fabric Explorer på Linux][sfx-linux]

Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästbehållare eller körbara gästprogram. Om du vill skapa nya tjänster med SDK:er för Java eller .NET Core, följer du installationsanvisningarna i följande avsnitt.


> [!NOTE]
> Fristående kluster stöds inte i Linux. Förhandsversionen stöder endast one-box-kluster och kluster på flera Azure Linux-datorer.
>

## <a name="install-the-java-sdk-optional-if-you-want-to-use-the-java-programming-models"></a>Installera Java SDK (valfritt om du vill använda Java-programmeringsmodeller)
Java SDK tillhandahåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med Java.

1. Installera Java SDK-paketet.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Installera Eclipse Neon-plugin-programmet (valfritt)

Du kan installera Eclipse-plugin-programmet för Service Fabric i **Eclipse IDE för Java-utvecklare**. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och behållarprogram utöver Service Fabric Java-program.

> [!NOTE]
> Java SDK är ett krav för att kunna använda Eclipse-plugin-programmet, även om du bara använder det för körbara gästprogram och behållarprogram.
>

1. Kontrollera att du har den senaste Eclipse Neon-versionen och den senaste Buildship-versionen (1.0.17 eller senare) installerat. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Hjälp** > **Installationsinformation**. Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update].

2. Om du vill installera Service Fabric-plugin-programmet väljer du **Hjälp** > **Installera ny programvara**.

3. Ange **http://dl.microsoft.com/eclipse** i textrutan **Arbeta med**.

4. Klicka på **Lägg till**.

    ![Sidan Tillgänglig programvara][sf-eclipse-plugin]

5. Välj **Service Fabric**-plugin-programmet och klicka sedan på **Nästa**.

6. Slutför installationsstegen och acceptera licensavtalet för användare.

Om du redan har Service Fabric Eclipse-plugin-programmet installerat kontrollerar du att du har den senaste versionen. Du kan kontrollera detta genom att välja **Hjälp** > **Installationsinformation** och sedan söka efter Service Fabric i listan över installerade plugin-program. Välj **Uppdatera** om det finns en nyare version.

Mer information finns i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Installera .NET Core SDK (valfritt, om du vill använda .NET Core-programmeringsmodeller)
.NET Core SDK innehåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med .NET Core.

1. Installera .NET Core SDK-paketet.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Kör SDK-installationsskriptet.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="update-the-sdk-and-runtime"></a>Uppdatera SDK och Runtime

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande kommandon (avmarkera de SDK:er från listan som du inte vill ha):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> När du uppdaterar paketen ovan kan ditt lokala miljökluster stoppas. Starta om ditt lokala kluster efter en uppgradering genom att följa instruktionerna på den här sidan.

## <a name="next-steps"></a>Nästa steg
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Använd XPlat CLI för att hantera dina Service Fabric-program](service-fabric-azure-cli.md)
* [Skillnader mellan Service Fabric i Windows och Linux](service-fabric-linux-windows-differences.md)

## <a name="related-articles"></a>Relaterade artiklar

* [Kom igång med Service Fabric och Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Kom igång med Service Fabric XPlat CLI](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

