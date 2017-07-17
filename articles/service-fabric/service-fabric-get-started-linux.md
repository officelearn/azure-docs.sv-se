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
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 5840932d79ef9fd99a94eb6ae4e587b0e616065e
ms.contentlocale: sv-se
ms.lasthandoff: 07/01/2017


---
# Förbereda utvecklingsmiljön i Linux
<a id="prepare-your-development-environment-on-linux" class="xliff"></a>
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java och .NET Core.

## Krav
<a id="prerequisites" class="xliff"></a>

### Operativsystemversioner som stöds
<a id="supported-operating-system-versions" class="xliff"></a>
Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (`Xenial Xerus`)

## Uppdatera dina APT-källor
<a id="update-your-apt-sources" class="xliff"></a>
Om du vill installera SDK och tillhörande runtime-paket via apt-get måste du först uppdatera dina APT-källor.

1. Öppna en terminal.
2. Lägga till Service Fabric-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Lägg till `dotnet`-repon i listan med källor.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Lägg till den nya GPG-nyckeln i APT-nyckelringen.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Lägg till Dockers officiella GPG nyckel i din apt-nyckelring.

    ```bash
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

## Installera och konfigurera SDK för behållare och körbara gästprogram
<a id="install-and-set-up-the-sdk-for-containers-and-guest-executables" class="xliff"></a>

När källorna har uppdaterats kan du installera SDK.

1. Installera Service Fabric SDK-paketet. Du får bekräfta installationen och godkänna ett licensavtal.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Följande kommandon automatiserar godkännandet av licensen för Service Fabric-paket:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    >   ```
    
2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

När du har kört stegen för att installera det vanliga SDK-paketet kan du skapa appar med körbara gästprogram eller behållartjänster genom att köra `yo azuresfguest` eller `yo azuresfcontainer`. Du kan behöva ange **$NODE_PATH**-miljövariabeln till den plats där nodmodulerna finns. 


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

## Konfigurera XPlat Service Fabric CLI
<a id="setup-the-xplat-service-fabric-cli" class="xliff"></a>
[XPlat CLI][azure-xplat-cli-github] innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Det är baserat på Node.js. Det innebär att du måste [kontrollera att du har installerat Node][install-node] innan du fortsätter med följande instruktioner:

1. Klona github-repon till din utvecklingsdator.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Växla till den klonade repon och installera CLI:s beroenden med NPM (Node Package Manager).

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

### Ställ in Azure CLI 2.0
<a id="setup-azure-cli-20" class="xliff"></a>

Som ett alternativ till XPlat CLI finns nu en modul för Service Fabric-kommandot som ingår i Azure CLI.

Mer information om att installera Azure CLI 2.0 och använda Service Fabric-kommandon finns i [Bruksanvisningen](service-fabric-azure-cli-2-0.md).

## Konfigurera ett lokalt kluster
<a id="set-up-a-local-cluster" class="xliff"></a>
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

## Installera Java SDK (valfritt, om du vill använda Java-programmeringsmodeller)
<a id="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models" class="xliff"></a>
Java SDK tillhandahåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med Java.

1. Installera Java SDK-paketet.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Kör SDK-installationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## Installera Eclipse Neon-plugin-programmet (valfritt)
<a id="install-the-eclipse-neon-plugin-optional" class="xliff"></a>

Du kan installera Eclipse-plugin-programmet för Service Fabric i **Eclipse IDE för Java-utvecklare**. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och behållarprogram utöver Service Fabric Java-program.

> [!NOTE]
> Java SDK är ett krav för att kunna använda Eclipse-plugin-programmet, även om du bara använder det för körbara gästprogram och behållarprogram.
>

1. Kontrollera att du har den senaste Eclipse **Neon**-versionen och den senaste Buildship-versionen (1.0.17 eller senare) installerat. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help > Installation Details** (Hjälp > Installationsinformation). Du kan uppdatera Buildship genom att följa instruktionerna [här][buildship-update].
2. Om du vill installera Service Fabric-plugin-programmet väljer du **Help > Install New Software...** (Hjälp > Installera ny programvara...).
3. Ange http://dl.microsoft.com/eclipse i textrutan ”Arbeta med”
4. Klicka på Add (Lägg till).

    ![Eclipse-plugin-program][sf-eclipse-plugin]

5. Välj Service Fabric-plugin-programmet och klicka på **Next** (Nästa).
6. Fortsätt med installationen och acceptera licensvillkoren.

Om du redan har plugin-programmet för Service Fabric Eclipse installerat kontrollerar du att du har den senaste versionen. Du kan kontrollera detta genom att välja ``Help => Installation Details`` och söka efter Service Fabric i listan över installerade plugin-program. Välj att uppdatera om det finns en nyare version.

Mer information finns i [Komma igång med Service Fabric i Eclipse](service-fabric-get-started-eclipse.md).


## Installera .NET Core SDK (valfritt, om du vill använda .NET Core-programmeringsmodeller)
<a id="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models" class="xliff"></a>
.NET Core SDK innehåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med .NET Core.

1. Installera .NET Core SDK-paketet.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Kör SDK-installationsskriptet.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## Uppdatera SDK och Runtime
<a id="updating-the-sdk-and-runtime" class="xliff"></a>

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande kommandon (avmarkera de SDK:er från listan som du inte vill ha):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> När du uppdaterar paketen ovan kan ditt lokala miljökluster stoppas. Starta om ditt lokala kluster efter en uppgradering genom att följa instruktionerna på den här sidan.

## Nästa steg
<a id="next-steps" class="xliff"></a>
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Använd XPlat CLI för att hantera dina Service Fabric-program](service-fabric-azure-cli.md)
* [Skillnader mellan Service Fabric i Windows och Linux](service-fabric-linux-windows-differences.md)

## Relaterade artiklar
<a id="related-articles" class="xliff"></a>

* [Komma igång med Service Fabric och Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Komma igång med Service Fabric XPlat CLI](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

