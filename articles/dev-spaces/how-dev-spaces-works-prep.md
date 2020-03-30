---
title: Så här förbereder du ett projekt för Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver hur förberedelserna av projektet med Azure Dev Spaces fungerar
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241639"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Så här förbereder du ett projekt för Azure Dev Spaces

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team i ett AKS-kluster (Azure Kubernetes Service). Dev Spaces kan generera Dockerfiles och Helm-diagram för ditt projekt. Dev Spaces skapar och använder också en konfigurationsfil för distribution, körning och felsökning av Kubernetes-program i AKS. Alla dessa filer finns med programmets kod och kan läggas till i versionskontrollsystemet.

I den här artikeln beskrivs vad som händer med att förbereda projektet för att köras i AKS med Dev Spaces.

## <a name="prepare-your-code"></a>Förbered koden

För att kunna köra ditt program i ett dev-utrymme måste det behållas och du måste definiera hur det ska distribueras till Kubernetes. Om du vill behålla programmet behöver du en Dockerfile. Om du vill definiera hur programmet distribueras till Kubernetes behöver du ett [Helm-diagram](https://docs.helm.sh/). För att hjälpa till att skapa både Dockerfile- och Helm-diagrammet för ditt program, ger klientverktygen `prep` kommandot:

```cmd
azds prep --enable-ingress
```

Kommandot `prep` kommer att titta på filerna i projektet och försöka skapa Dockerfile och Helm-diagrammet för att köra ditt program i Kubernetes. För närvarande `prep` genererar kommandot ett Dockerfile- och Helm-diagram med följande språk:

* Java
* Node.js
* .NET Core

Du *måste* `prep` köra kommandot från en katalog som innehåller källkod. Om `prep` du kör kommandot från rätt katalog kan verktyget på klientsidan identifiera språket och skapa en lämplig Dockerfile för att behålla programmet. Du kan också `prep` köra kommandot från en katalog som innehåller en *pom.xml-fil* för Java-projekt.

Om du `prep` kör kommandot från katalogen som inte innehåller källkod genererar verktyget på klientsidan inte en Dockerfile. Det kommer också att visa ett fel som säger: *Dockerfile kunde inte genereras på grund av språk som inte stöds*. Det här felet uppstår också om verktygen på klientsidan inte känner igen projekttypen.

När du `prep` kör kommandot kan du välja `--enable-ingress` att ange flaggan. Den här flaggan talar om för styrenheten att skapa en internettillgänglig slutpunkt för den här tjänsten. Om du inte anger den här flaggan är tjänsten endast tillgänglig inifrån klustret eller med hjälp av den localhost-tunnel som skapats av klientverktyget. Du kan aktivera eller inaktivera `prep` det här beteendet när du har kört kommandot genom att uppdatera det genererade Helm-diagrammet.

Kommandot `prep` ersätter inte befintliga Dockerfiles- eller Helm-diagram som du har i projektet. Om ett befintligt Dockerfile- eller Helm-diagram använder samma `prep` namngivningskonvention som de filer som genereras av kommandot, hoppar `prep` kommandot över att generera dessa filer. Annars genererar `prep` kommandot ett eget Dockerfile- eller Helm-diagram längs sidan av de befintliga filerna.

> [!IMPORTANT]
> Azure Dev Spaces använder Dockerfile- och Helm-diagrammet för att projektet ska kunna skapa och köra koden, men du kan ändra dessa filer om du vill ändra hur projektet byggs och körs.

Kommandot `prep` genererar också `azds.yaml` en fil i projektets rot. Azure Dev Spaces använder den här filen för att skapa, installera, konfigurera och köra ditt program. Den här konfigurationsfilen visar platsen för Dockerfile- och Helm-diagrammet och ger även ytterligare konfiguration ovanpå dessa artefakter.

Här är ett exempel azds.yaml fil som skapats med [.NET Core exempelprogram:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
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
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
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
```

Filen `azds.yaml` som genereras `prep` av kommandot är avsedd att fungera för enkla, enda projektutvecklingsscenario. Om ditt specifika projekt har ökat komplexiteten kan du `prep` behöva uppdatera filen efter att ha kört kommandot. Projektet kan till exempel kräva vissa ändringar i bygg- eller startprocessen baserat på dina utvecklings- eller felsökningsbehov. Du kan också ha flera program i projektet, som kräver flera byggprocesser eller ett annat bygginnehåll.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kör koden i ditt utvecklingsutrymme finns i [Så här fungerar du när du kör koden med Azure Dev Spaces][how-it-works-up].

Information om hur du kommer igång med Azure Dev Spaces för att förbereda projektet för Azure Dev Space finns i följande snabbstarter:

* [Snabbt iterera och felsöka med Visual Studio Code och Java][quickstart-java]
* [Snabbt iterera och felsöka med Visual Studio-kod och .NET][quickstart-netcore]
* [Snabbt iterera och felsöka med Visual Studio-kod och Node.js][quickstart-node]
* [Snabbt iterera och felsöka med Visual Studio och .NET Core][quickstart-vs]
* [Använda CLI för att utveckla ett program på Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md