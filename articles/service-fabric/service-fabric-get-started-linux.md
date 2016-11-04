---
title: Konfigurera en utvecklingsmiljö i Linux | Microsoft Docs
description: Installera runtime och SDK, och skapa ett lokalt utvecklingskluster i Linux. När du har slutfört den här installationen är du redo att börja bygga program.
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: seanmck

---
# Förbereda utvecklingsmiljön i Linux
> [!div class="op_single_selector"]
> -[ Windows](service-fabric-get-started.md)
> 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java och .NET Core.

## Krav
### Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16.04 (Xenial Xerus)

## Uppdatera dina APT-källor
Om du vill installera SDK och tillhörande runtime-paket via apt-get måste du först uppdatera dina APT-källor.

1. Öppna en terminal.
2. Lägga till Service Fabric-repon i listan med källor.
   
    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Lägg till den nya GPG-nyckeln i APT-nyckelringen.
   
    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
4. Uppdatera paketlistor baserat på nyligen tillagda lagringsplatser.
   
    ```bash
    sudo apt-get update
    ```

## Installera och konfigurera SDK
När källorna har uppdaterats kan du installera SDK.

1. Installera Service Fabric SDK-paketet. Du får bekräfta installationen och godkänna ett licensavtal.
   
    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Kör SDK-installationsskriptet.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## Konfigurera Azures plattformsoberoende CLI
[Azures plattformsoberoende CLI][azure-xplat-cli-github] innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Det är baserat på Node.js. Det innebär att du måste [installera Node][install-node] innan du fortsätter med instruktionerna nedan.

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

## Konfigurera ett lokalt kluster
Om allt har installerats korrekt kan du starta ett lokalt kluster.

1. Kör klusterinstallationsskriptet.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Öppna webbläsaren och gå till http://localhost:19080/Explorer. Om klustret har startats visas instrumentpanelen Service Fabric Explorer.
   
    ![Service Fabric Explorer på Linux][sfx-linux]

Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästbehållare eller körbara gästprogram. Om du vill skapa nya tjänster med SDK för Java eller .NET Core följer du installationsanvisningarna nedan.

## Installera Java SDK och Eclipse Neon-plugin-programmet (valfritt)
Java SDK tillhandahåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med Java.

1. Installera Java SDK-paketet.
   
    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Kör SDK-installationsskriptet.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Du kan installera Eclipse-plugin-programmet för Service Fabric i Eclipse Neon IDE.

1. Kontrollera att du har Buildship version 1.0.17 eller senare installerat i Eclipse. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help > Installation Details** (Hjälp > Installationsinformation). Du kan uppdatera Buildship med instruktionerna som finns [här][buildship-update].
2. Om du vill installera Service Fabric-plugin-programmet väljer du **Help > Install New Software...** (Hjälp > Installera ny programvara...).
3. Ange följande i textrutan Work with (Arbeta med): http://dl.windowsazure.com/eclipse/servicefabric
4. Klicka på Add (Lägg till).
   
    ![Eclipse-plugin-program][sf-eclipse-plugin]
5. Välj Service Fabric-plugin-programmet och klicka på Next (Nästa).
6. Fortsätt med installationen och acceptera licensvillkoren.

## Installera .NET Core SDK (valfritt)
.NET Core SDK innehåller de bibliotek och mallar som krävs för att skapa Service Fabric-tjänster med plattformsoberoende .NET Core.

1. Installera .NET Core SDK-paketet.
   
    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```
2. Kör SDK-installationsskriptet.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## Nästa steg
* [Skapa ditt första Java-program i Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png



<!--HONumber=Sep16_HO4-->


