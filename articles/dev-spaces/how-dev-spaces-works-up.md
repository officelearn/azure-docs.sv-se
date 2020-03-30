---
title: Så här fungerar körningen av koden med Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processerna för att köra din kod på Azure Kubernetes Service med Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241366"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Så här fungerar körningen av koden med Azure Dev Spaces

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team i ett AKS-kluster (Azure Kubernetes Service). När [projektet är förberett för att köras i ett utvecklingsutrymme][how-it-works-prep]kan du använda Dev Spaces för att skapa och köra projektet i AKS-klustret.

I den här artikeln beskrivs vad som händer kör din kod i AKS med Dev Spaces.

## <a name="run-your-code"></a>Kör koden

Om du vill köra koden i `up` ett utvecklingsutrymme utfärdar du kommandot i samma katalog som filen: `azds.yaml`

```cmd
azds up
```

Kommandot `up` överför programkällafiler och andra artefakter som behövs för att skapa och köra projektet till utvecklingsutrymmet. Därifrån, handkontrollen i din dev utrymme:

1. Skapar Kubernetes-objekten för att distribuera ditt program.
1. Skapar behållaren för ditt program.
1. Distribuerar ditt program till utvecklingsutrymmet.
1. Skapar ett offentligt DNS-namn för programslutpunkten om det är konfigurerat.
1. Använder *port-forward* för att ge åtkomst http://localhosttill programslutpunkten med .
1. Framåt stdout och stderr till klientsidan verktyg.


## <a name="starting-a-service"></a>Starta en tjänst

När du startar en tjänst i ett utvecklingsutrymme arbetar verktyg och styrenhet på klientsidan för att synkronisera källfilerna, skapa dina behållare- och Kubernetes-objekt och köra programmet.

På en mer detaljerad nivå, här är `azds up`vad som händer när du kör:

1. [Filer synkroniseras][sync-section] från användarens dator till en Azure-fillagring som är unik för användarens AKS-kluster. Källkoden, Helm-diagrammet och konfigurationsfilerna laddas upp.
1. Styrenheten skapar en begäran om att starta en ny session. Den här begäran innehåller flera egenskaper, inklusive ett unikt ID, utrymmesnamn, sökväg till källkod och en felsökningsflagga.
1. Styrenheten ersätter platshållaren *$(tag)* i Helm-diagrammet med det unika sessions-ID:t och installerar Helm-diagrammet för din tjänst. Om du lägger till en referens till det unika sessions-ID:t i Helm-diagrammet kan behållaren som distribueras till AKS-klustret för den här specifika sessionen kopplas tillbaka till sessionsbegäran och tillhörande information.
1. Under installationen av Helm-diagrammet lägger Kubernetes webhook-entréservern till ytterligare behållare i programmets pod för instrumentering och åtkomst till projektets källkod. Devspaces-proxy- och devspaces-proxy-init-behållarna läggs till för att tillhandahålla HTTP-spårning och utrymmesroutning. Behållaren devspaces-build läggs till för att ge podden åtkomst till Docker-instansen och projektkällan för att skapa programmets behållare.
1. När programmets pod startas används behållaren devspaces-build och devspaces-proxy-init för att skapa programbehållaren. Programbehållaren och devspaces-proxy-behållarna startas sedan.
1. När programbehållaren har startat använder funktionen kubernetes *port-forward* för att ge HTTP-åtkomst http://localhosttill ditt program över . Den här portåtriktningen ansluter utvecklingsdatorn till tjänsten i utvecklingsutrymmet.
1. När alla behållare i kapseln har startat körs tjänsten. Nu börjar klientfunktionen strömma HTTP-spårningar, stdout och stderr. Den här informationen visas av utvecklarens funktioner på klientsidan.

## <a name="updating-a-running-service"></a>Uppdatera en tjänst som körs

Medan en tjänst körs har Azure Dev Spaces möjlighet att uppdatera den tjänsten om någon av projektkällan filer ändras. Dev Spaces hanterar också uppdateringen av tjänsten på olika sätt beroende på vilken typ av fil som ändras. Det finns tre sätt dev spaces kan uppdatera en tjänst som körs:

* Direkt uppdatera en fil
* Återskapa och starta om programmets process i programmets behållare som körs
* Ombyggnad och omfördelning av programmets behållare

![Azure Dev Spaces-filsynkronisering](media/how-dev-spaces-works/file-sync.svg)

Vissa projektfiler som är statiska tillgångar, till exempel html-, css- och cshtml-filer, kan uppdateras direkt i programmets behållare utan att starta om något. Om en statisk tillgång ändras synkroniseras den nya filen med utvecklingsutrymmet och används sedan av den behållare som körs.

Ändringar av filer som källkod eller programkonfigurationsfiler kan tillämpas genom att starta om programmets process i behållaren som körs. När dessa filer har synkroniserats startas programmets process om i behållaren som körs med *devhostagent-processen.* När du först skapar programmets behållare ersätter styrenheten startkommandot för programmet med en annan process som kallas *devhostagent*. Programmets faktiska process körs sedan som en underordnad process under *devhostagent*, och dess utdata är piped ut med *devhostagent*'s utdata. *Devhostagent-processen* är också en del av Dev Spaces och kan köra kommandon i behållaren som körs på uppdrag av Dev Spaces. När du utför en omstart, *devhostagent:*

* Stoppar den aktuella processen eller processer som är associerade med programmet
* Återskapar programmet
* Startar om processen eller processerna som är associerade med programmet

Hur *devhostagent* kör föregående steg [konfigureras `azds.yaml`i ][azds-yaml-section].

Uppdateringar av projektfiler som Dockerfiles, csproj-filer eller någon del av Helm-diagrammet kräver att programmets behållare återskapas och distribueras om. När en av dessa filer synkroniseras med utvecklingsutrymmet kör styrenheten kommandot [helm upgrade][helm-upgrade] och programmets behållare återskapas och distribueras om.

## <a name="file-synchronization"></a>Synkronisering av filer

Första gången ett program startas i ett utvecklingsutrymme överförs alla programmets källfiler. Medan programmet körs och senare startas om överförs endast de ändrade filerna. Två filer används för att samordna den här processen: en klientfil och en controller-side-fil.

Klientfilen lagras i en tillfällig katalog och namnges baserat på ett hash-kort i projektkatalogen som du kör i Dev Spaces. I Windows skulle du till exempel ha en fil som *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* för ditt projekt. På Linux lagras klientfilen i katalogen */tmp.* Du hittar katalogen på macOS `echo $TMPDIR` genom att köra kommandot.

Den här filen är i JSON-format och innehåller:

* En post för varje projektfil som synkroniseras med utvecklingsutrymmet
* Ett synkroniserings-ID
* Tidsstämpeln för den senaste synkroniseringsåtgärden

Varje projektfilpost innehåller en sökväg till filen och dess tidsstämpel.

Styrenhetsfilen lagras i AKS-klustret. Den innehåller synkroniserings-ID och tidsstämpeln för den senaste synkroniseringen.

En synkronisering inträffar när synkroniseringstidsstämplarna inte matchar mellan klientsidan och filer på styrenhetens sida. Under en synkronisering iterar du på klientsidan över filposterna i klientfilen. Om filens tidsstämpel är efter synkroniseringstidsstämpeln synkroniseras filen med utvecklingsutrymmet. När synkroniseringen är klar uppdateras synkroniseringstidsstämplarna på både klient- och controller-sidans filer.

Alla projektfiler synkroniseras om klientfilen inte finns. Med det här fallet kan du tvinga fram en fullständig synkronisering genom att ta bort klientfilen.

## <a name="how-running-your-code-is-configured"></a>Så här konfigureras koden när du kör

Azure Dev Spaces `azds.yaml` använder filen för att installera och konfigurera tjänsten. Styrenheten använder `install` egenskapen `azds.yaml` i filen för att installera Helm-diagrammet och skapa Kubernetes-objekten:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Som standard `prep` genererar kommandot Helm-diagrammet. Egenskapen *install.chart* ställs också in på katalogen för Helm-diagrammet. Om du vill använda ett Helm-diagram på en annan plats kan du uppdatera den här egenskapen så att den platsen används.

När du installerar Helm-diagrammen är Azure Dev Spaces ett sätt att åsidosätta värden i Helm-diagrammet. Standardvärdena för Helm-diagrammet `charts/APP_NAME/values.yaml`finns i .

Med egenskapen *install.values* kan du lista en eller flera filer som definierar värden som du vill ersätta i Helm-diagrammet. Om du till exempel vill ha ett värdnamn eller en databaskonfiguration specifikt när du kör programmet i ett utvecklingsutrymme kan du använda den här åsidosättningsfunktionen. Du kan också lägga till en *?* i slutet av något av filnamnen för att ställa in det som valfritt.

Med egenskapen *install.set* kan du konfigurera ett eller flera värden som du vill byta ut i Helm-diagrammet. Alla värden som konfigurerats i *install.set* åsidosätter värden som konfigurerats i filer som anges i *install.values*. Egenskaperna under *install.set* är beroende av värdena i Helm-diagrammet och kan vara olika beroende på det genererade Helm-diagrammet.

I exemplet ovan anger egenskapen *install.set.replicaCount* för styrenheten hur många instanser av programmet som ska köras i utvecklingsutrymmet. Beroende på ditt scenario kan du öka det här värdet, men det påverkar hur du kopplar en felsökare till programmets pod. Mer information finns i [felsökningsartikeln][troubleshooting].

I det genererade Helm-diagrammet är behållaravbildningen inställd *på {{ . Värden.image.repository }}:{{ . Values.image.tag }}*. Filen `azds.yaml` definierar egenskapen *install.set.image.tag* som *egenskapen $(tag)* som standard, som används som värde för *{{ . Values.image.tag }}*. Genom att ange egenskapen *install.set.image.tag* på det här sättet kan behållaravbildningen för ditt program taggas på ett tydligt sätt när du kör Azure Dev Spaces. I det här specifika fallet taggas bilden som * \<värde från image.repository>:$(tag)*. Du måste använda variabeln *$(tag)* som värdet för *install.set.image.tag* för att Dev Spaces ska känna igen och hitta behållaren i AKS-klustret.

I exemplet ovan `azds.yaml` definierar *install.set.ingress.hosts*. Egenskapen *install.set.ingress.hosts* definierar ett värdnamnsformat för offentliga slutpunkter. Den här egenskapen använder också *$(spacePrefix),* *$(rootSpacePrefix)* och *$(hostSuffix),* som är värden som tillhandahålls av styrenheten.

*$(spacePrefix)* är namnet på det underordnade dev-utrymmet, som har formen av *SPACENAME.s*. *$(rootSpacePrefix)* är namnet på det överordnade utrymmet. Om *azureuser* till exempel är ett *underordnat*utrymme för standard är värdet för *$(rootSpacePrefix)* *standard* och värdet *på $(spacePrefix)* är *azureuser.s*. Om utrymmet inte är ett underordnat utrymme är *$(spacePrefix)* tomt. Om *standardutrymmet* till exempel inte har något överordnat utrymme är värdet för *$(rootSpacePrefix)* *standard* och värdet *$(spacePrefix)* är tomt. *$(hostSuffix)* är ett DNS-suffix som pekar på Azure Dev Spaces Ingress Controller som körs i AKS-klustret. Det här DNS-suffixet motsvarar till exempel * \*en DNS-post med jokertecken. RANDOM_VALUE.eus.azds.io*, som skapades när Azure Dev Spaces-styrenheten lades till i AKS-klustret.

I filen `azds.yaml` ovan kan du också uppdatera *install.set.ingress.hosts* för att ändra värdnamnet för ditt program. Om du till exempel vill förenkla värdnamnet för ditt program från *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* till *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

För att skapa behållaren för ditt program använder `azds.yaml` styrenheten nedanstående avsnitt i konfigurationsfilen:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Styrenheten använder en Dockerfile för att skapa och köra ditt program.

Egenskapen *build.context* listar katalogen där Dockerfiler finns. Egenskapen *build.dockerfile* definierar namnet på Dockerfile för att skapa produktionsversionen av programmet. Egenskapen *configurations.develop.build.dockerfile* konfigurerar namnet på Dockerfile för utvecklingsversionen av programmet.

Med olika Dockerfiles för utveckling och produktion kan du aktivera vissa saker under utveckling och inaktivera dessa artiklar för produktionsdistributioner. Du kan till exempel aktivera felsökning eller mer utförlig loggning under utveckling och inaktivera i en produktionsmiljö. Du kan också uppdatera dessa egenskaper om Dockerfilerna namnges på olika sätt eller finns på en annan plats.

För att hjälpa dig att snabbt iterera under utvecklingen synkroniserar Azure Dev Spaces ändringar från ditt lokala projekt och uppdaterar ditt program stegvis. Avsnittet nedan i `azds.yaml` konfigurationsfilen används för att konfigurera synkroniseringen och uppdateringen:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

De filer och kataloger som synkroniseras visas i egenskapen *configurations.develop.container.sync.* Dessa kataloger synkroniseras från början `up` när du kör kommandot samt när ändringar identifieras. Om det finns ytterligare eller olika kataloger som du vill synkroniseras med ditt utvecklingsutrymme kan du ändra den här egenskapen.

Egenskapen *configurations.develop.container.iterate.buildCommands* anger hur programmet ska skapas i ett utvecklingsscenario. Egenskapen *configurations.develop.container.command* ger kommandot för att köra programmet i ett utvecklingsscenario. Du kanske vill uppdatera någon av dessa egenskaper om det finns ytterligare bygg- eller körningsflaggor eller parametrar som du vill använda under utvecklingen.

*Configurations.develop.container.iterate.processesToKill* listar de processer som ska dödas för att stoppa programmet. Du kanske vill uppdatera den här egenskapen om du vill ändra omstartsbeteendet för ditt program under utvecklingen. Om du till exempel har uppdaterat *egenskaperna configurations.develop.container.iterate.buildCommands* eller *configurations.develop.container.command* för att ändra hur programmet byggs eller startas, kan du behöva ändra vilka processer som stoppas.

När du förbereder `azds prep` koden med kommandot kan du `--enable-ingress` välja att lägga till flaggan. Om `--enable-ingress` du lägger till flaggan skapas en url som är allmänt tillgänglig för ditt program. Om du utelämnar den här flaggan är programmet endast tillgängligt i klustret eller med hjälp av den lokala värdtunneln. När du `azds prep` har kört kommandot kan du ändra den här inställningen `charts/APPNAME/values.yaml`ändra egenskapen *ingress.enabled* i :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverk och hur begäranden dirigeras i Azure Dev Spaces finns [i Hur routning fungerar med Azure Dev Spaces][how-it-works-routing].

Mer information om hur du använder Azure Dev Spaces för att snabbt iterera och utveckla finns i [Hur det fungerar att ansluta utvecklingsdatorn till utvecklingsutrymmet][how-it-works-connect] och hur [fjärrfelsökning av koden med Azure Dev Spaces fungerar][how-it-works-remote-debugging].

Information om hur du kommer igång med Azure Dev Spaces för att köra projektet finns i följande snabbstarter:

* [Snabbt iterera och felsöka med Visual Studio Code och Java][quickstart-java]
* [Snabbt iterera och felsöka med Visual Studio-kod och .NET][quickstart-netcore]
* [Snabbt iterera och felsöka med Visual Studio-kod och Node.js][quickstart-node]
* [Snabbt iterera och felsöka med Visual Studio och .NET Core][quickstart-vs]
* [Använda CLI för att utveckla ett program på Kubernetes][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md