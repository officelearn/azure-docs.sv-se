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
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 516b8e517a16dd0d87e02189260166696225fbab
ms.lasthandoff: 03/28/2017


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

### <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (i**"Xenial Xerus"**)

## <a name="update-your-apt-sources"></a>Uppdatera dina APT-källor
Om du vill installera SDK och tillhörande runtime-paket via apt-get måste du först uppdatera dina APT-källor.

1. Öppna en terminal.
2. Lägga till Service Fabric-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Lägg till **dotnet**-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Lägg till den nya GPG-nyckeln i APT-nyckelringen.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Uppdatera paketlistor baserat på nyligen tillagda lagringsplatser.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Installera och konfigurera SDK för behållare och körbara gästprogram
När källorna har uppdaterats kan du installera SDK.

1. Installera Service Fabric SDK-paketet. Du får bekräfta installationen och godkänna ett licensavtal.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

När du har kört stegen för att installera det vanliga SDK-paketet kan du skapa appar med körbara gästprogram eller behållartjänster genom att köra `yo azuresfguest`. Du kan behöva ange **$NODE_PATH**-miljövariabeln till den plats där nodmodulerna finns. 

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

## <a name="set-up-the-azure-cross-platform-cli"></a>Konfigurera Azures plattformsoberoende CLI
[Azures plattformsoberoende CLI][azure-xplat-cli-github] innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Det är baserat på Node.js. Det innebär att du måste [kontrollera att du har installerat Node][install-node] innan du fortsätter med följande instruktioner:

1. Klona github-repon till din utvecklingsdator.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Växla till den klonade repon och installera CLI:s beroenden med NPM (Node Package Manager).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Skapa en symlink från mappen bin/azure på den klonade repon till /usr/bin/azure så att den läggs till i din sökväg och kommandona blir tillgängliga från alla kataloger.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Slutligen aktiverar du Service Fabric-kommandon för automatisk komplettering.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Service Fabric-kommandon är inte tillgängliga i Azure CLI 2.0 ännu.


## <a name="set-up-a-local-cluster"></a>Konfigurera ett lokalt kluster
Om allt har installerats korrekt kan du starta ett lokalt kluster.

1. Kör klusterinstallationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Öppna webbläsaren och gå till http://localhost:19080/Explorer. Om klustret har startats visas instrumentpanelen Service Fabric Explorer.

    ![Service Fabric Explorer på Linux][sfx-linux]

Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästbehållare eller körbara gästprogram. Om du vill skapa nya tjänster med SDK för Java eller .NET Core, följer du installationsanvisningarna i följande avsnitt.


> [!NOTE]
> Fristående kluster stöds inte i Linux - endast en ruta och Azure Linux-multidatorkluster stöds i förhandsvisningen.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Installera Java SDK (valfritt, om du vill använda Java-programmeringsmodeller)
Java SDK tillhandahåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med Java.

1. Installera Java SDK-paketet.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Installera Eclipse Neon-plugin-programmet (valfritt)

Du kan installera Eclipse-plugin-programmet för Service Fabric i **Eclipse IDE för Java-utvecklare**. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och behållarprogram utöver Service Fabric Java-program.

> [!NOTE]
> Du måste installera Java SDK för att kunna använda Eclipse-plugin-programmet, även om du bara använder det för att distribuera körbara gästprogram och behållarprogram.
>

1. Kontrollera att du har den senaste Eclipse **Neon**-versionen och den senaste Buildship-versionen (1.0.17 eller senare) installerat. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help > Installation Details** (Hjälp > Installationsinformation). Du kan uppdatera Buildship genom att följa instruktionerna [här][buildship-update].
2. Om du vill installera Service Fabric-plugin-programmet väljer du **Help > Install New Software...** (Hjälp > Installera ny programvara...).
3. Ange följande i textrutan Work with (Arbeta med): http://dl.windowsazure.com/eclipse/servicefabric
4. Klicka på Add (Lägg till).
    ![Plugin-program för Eclipse][sf-eclipse-plugin]
5. Välj Service Fabric-plugin-programmet och klicka på Next (Nästa).
6. Fortsätt med installationen och acceptera licensvillkoren.

Om du redan har plugin-programmet för Service Fabric Eclipse installerat kontrollerar du att du har den senaste versionen. Du kan kontrollera detta genom att välja ``Help => Installation Details`` och söka efter Service Fabric i listan över installerade plugin-program. Välj att uppdatera om det finns en nyare version. 

Mer information finns i [Komma igång med Service Fabric i Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Installera .NET Core SDK (valfritt, om du vill använda .NET Core-programmeringsmodeller)
.NET Core SDK innehåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med plattformsoberoende .NET Core.

1. Installera .NET Core SDK-paketet.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Kör SDK-installationsskriptet.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Uppdatera SDK och Runtime

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande steg (ta bort de SDK:er från listan som du inte vill uppdatera eller installera):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```

Om du vill uppdatera CLI går du till katalogen där du klonade CLI och kör `git pull` för att uppdatera.  Om det behövs ytterligare steg för att uppdatera anges dessa i Viktig information. 


## <a name="next-steps"></a>Nästa steg
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Använd Azure CLI för att hantera dina Service Fabric-program](service-fabric-azure-cli.md)
* [Skillnader mellan Service Fabric i Windows och Linux](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

