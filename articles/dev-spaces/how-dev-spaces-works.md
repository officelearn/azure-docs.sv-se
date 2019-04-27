---
title: Hur Azure Dev blanksteg fungerar och är konfigurerad
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: Beskriver processerna som power Azure Dev blanksteg och hur de konfigureras i konfigurationsfilen azds.yaml
keywords: azds.yaml Azure Dev blanksteg, Dev blanksteg, Docker, Kubernetes, Azure, AKS, Azure Kubernetes-tjänst, behållare
ms.openlocfilehash: 494dd3774ec47598a95c6e20de6283abc2e4ff94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687221"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Hur Azure Dev blanksteg fungerar och är konfigurerad

Utveckla ett Kubernetes-program kan vara svårt. Du måste Docker och Kubernetes konfigurationsfiler. Du måste ta reda på hur att testa ditt program lokalt och interagera med andra beroende tjänster. Du kan behöva hantera utveckla och testa på flera tjänster samtidigt, och med ett team med utvecklare.

Azure Dev blanksteg hjälper dig att utveckla, distribuera och Felsök Kubernetes-program direkt i Azure Kubernetes Service (AKS). Azure Dev blanksteg kan också ett team att dela en dev-utrymme. Dela en dev-utrymme mellan ett team kan enskilda gruppmedlemmarna att utveckla i isolering utan att replikera eller skapa simulerade beroenden eller andra program i klustret.

Azure Dev blanksteg skapar och använder en konfigurationsfil för att distribuera, köra och felsöka ditt Kubernetes-program i AKS. Den här konfigurationsfilen finns med din programkod och kan läggas till i din versionskontrollsystem.

Den här artikeln beskrivs processerna som power Azure Dev blanksteg och hur dessa processer har konfigurerats i konfigurationsfilen Azure Dev blanksteg. För att hämta Azure Dev blanksteg snabbt och se hur det i praktiken, gör du något av Snabbstart:

* [Java med CLI och Visual Studio Code](quickstart-java.md)
* [.NET core med CLI och Visual Studio Code](quickstart-netcore.md)
* [.NET core med Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js med CLI och Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Hur fungerar Azure Dev blanksteg

Azure Dev blanksteg har två olika komponenter som du interagerar med: styrenheten och verktyg för klientsidan.

![Azure Dev blanksteg-komponenter](media/how-dev-spaces-works/components.svg)

Kontrollanten utför följande åtgärder:

* Hanterar dev utrymme skapas och val av.
* Installerar programmets Helm-diagrammet och skapar Kubernetes-objekten.
* Skapar behållaravbildning för ditt program.
* Distribuerar programmet till AKS.
* Gör inkrementella versioner och startar om när källkoden ändras.
* Hanterar loggar och spårningar för HTTP.
* Vidarebefordrar stdout- och stderr verktygsuppsättningen på klientsidan.
* Tillåter gruppmedlemmar att skapa underordnade dev blanksteg som härletts från en överordnad dev utrymme.
* Konfigurerar Routning för program i ett blanksteg samt mellan flera överordnade och underordnade blanksteg.

Kontrollanten finns utanför AKS. Det styr beteende och kommunikation mellan verktyg för klientsidan och AKS-klustret. Kontrollanten har aktiverats med hjälp av Azure CLI när du förbereder att klustret ska använda Azure Dev blanksteg. När den har aktiverats kan du interagera med den med hjälp av verktyg för klientsidan.

Verktyg för klientsidan gör att användaren kan:
* Generera en Dockerfile Helm-diagrammet och Azure Dev blanksteg konfigurationsfil för programmet.
* Skapa överordnade och underordnade dev blanksteg.
* Berätta för kontrollanten för att skapa och starta programmet.

När programmet körs, klientsidan verktyg också:
* Tar emot och visar stdout och stderr från ditt program som körs i AKS.
* Använder [port och tydlig](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) att tillåta Webbåtkomst till ditt program via http:\//localhost.
* Bifogar en felsökare till programmet som körs i AKS.
* Synkroniseringar källkod ditt dev adressutrymme när en ändring identifieras för inkrementella versioner, vilket ger snabb iteration.

Du kan använda klientsidan verktyg från kommandoraden som en del av den `azds` kommando. Du kan också använda klientsidan verktyg med:

* Visual Studio Code med hjälp av den [Azure Dev blanksteg tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio 2017 med [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools).

Här är det grundläggande flödet för att konfigurera och använda Azure Dev blanksteg:
1. Förbered ditt AKS-kluster för Azure Dev blanksteg
1. Förbereda din kod för att köra på Azure Dev blanksteg
1. Köra din kod i en dev-utrymme
1. Felsöka kod på en dev-utrymme
1. Dela en dev-utrymme

Vi tar upp mer information om hur Azure Dev blanksteg fungerar i var och en av de nedan avsnitt.

## <a name="prepare-your-aks-cluster"></a>Förbered ditt AKS-kluster

Förbereda AKS-kluster omfattar:
* Verifiera ditt AKS-kluster som finns i en region [stöds av Azure Dev blanksteg](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).
* Verifiera att du kör Kubernetes 1.10.3 eller senare.
* Att aktivera Azure Dev blanksteg på ditt kluster med hjälp av `az aks use-dev-spaces`

Mer information om hur du skapar och konfigurerar ett AKS-kluster för Azure Dev blanksteg finns i komma igång-guider:
* [Kom igång med Azure Dev blankstegen med Java](get-started-java.md)
* [Kom igång med Azure Dev blankstegen med .NET Core och Visual Studio](get-started-netcore-visualstudio.md)
* [Kom igång med Azure Dev blanksteg med .NET Core](get-started-netcore.md)
* [Kom igång med Azure Dev blankstegen med Node.js](get-started-nodejs.md)

När Azure Dev blanksteg är aktiverat på AKS-klustret, installerar kontrollanten för klustret. Styrenheten är en separat Azure-resurs utanför klustret och gör följande för att resurser i klustret:

* Skapar eller anger en Kubernetes-namnområde som ska användas som en dev-utrymme.
* Tar bort alla Kubernetes namnområdes med namnet *azds*, om den finns, och skapar en ny.
* Distribuerar en konfiguration för Kubernetes-webhook.
* Distribuerar en webhook åtkomst-server.
    

Den använder även samma tjänstens huvudnamn som AKS-klustret använder för att göra de tjänstanrop till andra Azure Dev blanksteg-komponenter.

![Azure Dev blanksteg förbereda klustret](media/how-dev-spaces-works/prepare-cluster.svg)

För att kunna använda Azure Dev blanksteg, måste det finnas minst en dev-utrymme. Azure Dev blanksteg använder Kubernetes-namnområden i AKS-kluster för utveckling blanksteg. När en domänkontrollant installeras, uppmanas du att skapa ett nytt Kubernetes-namnområde eller välj ett befintligt namnområde ska användas som ditt första adressutrymme för utveckling. När ett namnområde är utsedd till en dev-utrymme, kontrollanten lägger till den *azds.io/space=true* etikett till det namnområdet för att identifiera den som en dev-utrymme. Inledande utveckling kan du skapa eller välja väljs som standard när du har förberett ditt kluster. När ett blanksteg väljs, används den med Azure Dev blanksteg för att skapa nya arbetsbelastningar.

Som standard skapar kontrollanten kan utveckling med namnet *standard* genom att uppgradera den befintliga *standard* Kubernetes namnområdes. Du kan använda klientsidan-verktyg för att skapa nya sidor för utveckling och ta bort den befintliga dev blanksteg. På grund av en begränsning i Kubernetes, den *standard* dev utrymme kan inte tas bort. Kontrollanten tar också bort alla befintliga Kubernetes-namnområden med namnet *azds* att undvika konflikter med den `azds` kommando som används av verktyg för klientsidan.

Kubernetes webhook åtkomst server används för att mata in poddar med tre behållare under distributionen för instrumentation: en devspaces-proxy-behållare, en devspaces-proxy-init-behållare och en devspaces-build-behållare. **Alla tre av de här behållarna kör med rotåtkomst AKS-klustret.** De kan också använda samma tjänstens huvudnamn som AKS-klustret använder för att göra de tjänstanrop till andra Azure Dev blanksteg-komponenter.

![Azure Dev blanksteg Kubernetes webhook åtkomst-server](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Behållaren devspaces-proxy är en sidovagnsbehållare som hanterar alla TCP-trafik till och från behållaren program och hjälper dig att utföra routning. Behållaren devspaces-proxy ska dras om HTTP-meddelanden om vissa blanksteg används. Det kan till exempel dirigera HTTP-meddelanden mellan program i överordnade och underordnade blanksteg. Alla icke-HTTP-trafik passerar genom devspaces-proxy ska ändras. Behållaren devspaces proxy också loggas alla inkommande och utgående HTTP-meddelanden och skickar dem till klientsidan verktyg som spårningar. De kan sedan visas av utvecklaren att inspektera programmets beteende.

Devspaces-proxy-init-behållaren är en [init behållare](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) som lägger till ytterligare regler för routning baserat på hierarkin utrymme till ditt programs behållare. Den lägger till regler för routning genom att uppdatera programmet behållarens */etc/resolv.conf* fil- och iptables konfiguration innan den startar. Uppdateringar av */etc/resolv.conf* tillåter DNS-matchning av tjänster i överordnade blanksteg. Konfigurationsuppdateringar iptables se till att alla TCP-trafik till och från programmets behållare dirigeras om devspaces-proxy. Alla uppdateringar från devspaces proxy initiering sker utöver de regler som lägger till Kubernetes.

Devspaces-build-behållaren är en init-behållare och har projekt källkod och Docker socket monterad. Källkoden för projektet och åtkomst till Docker kan programbehållare skapas direkt av din pod.

> [!NOTE]
> Azure Dev blanksteg använder samma nod för att skapa dina programbehållare och kör den. Azure Dev blanksteg behöver därför inte en extern container registry för att skapa och kör ditt program.

Kubernetes webhook åtkomst servern lyssnar efter eventuella nya pod som skapats i AKS-klustret. Om den poden distribueras till alla namnområden med den *azds.io/space=true* etikett, det lägger in den pod med ytterligare behållare. Devspaces-build-behållaren är endast matas in om programmets behållaren ska köras med hjälp av verktyg för klientsidan.

När du har förberett din AKS-kluster, kan du kan använda klientsidan-verktyg för att förbereda och köra din kod i ditt dev-adressutrymme.

## <a name="prepare-your-code"></a>Förbereda din kod

För att köra ditt program i en dev-utrymme, den måste vara behållare och du behöver definiera hur den ska distribueras till Kubernetes. Om du vill Använd ditt program i behållare, behöver du en Dockerfile. För att definiera hur programmet ska distribueras till Kubernetes, behöver du en [Helm-diagrammet](https://docs.helm.sh/). För att hjälpa skapa både Dockerfile och Helm-diagram för ditt program, verktyg för klientsidan ger den `prep` kommando:

```cmd
azds prep --public
```

Den `prep` kommando för att titta på filerna i projektet och försök att skapa Dockerfile och Helm-diagrammet för att köra ditt program i Kubernetes. För närvarande den `prep` kommandot genererar ett Dockerfile och Helm-diagram med följande språk:

* Java
* Node.js
* .NET Core

Du *måste* kör den `prep` från en katalog som innehåller källkod. Kör den `prep` kommandot från rätt katalog kan verktyg för klientsidan och identifiera språk och skapa en lämplig Dockerfile för att Använd ditt program i behållare. Du kan också köra den `prep` från en katalog som innehåller en *pom.xml* -filen för Java-projekt.

Om du kör den `prep` kommando från katalogen som inte innehåller källkoden, verktyg för klientsidan kommer inte att generera en Dockerfile. Ett fel som säger visas också: *Dockerfile kunde inte genereras på grund av språket stöds inte*. Det här felet uppstår också om projekttyp inte känner igen på klientsidan-verktyg.

När du kör den `prep` kommandot, har du möjlighet att ange den `--public` flaggan. Den här flaggan säger kontrollenheten för att skapa en internet-tillgänglig slutpunkt för den här tjänsten. Om du inte anger den här flaggan tjänsten är endast åtkomlig från inom klustret eller med hjälp av localhost-tunnel som skapats av klientsidan-verktyg. Du kan aktivera eller inaktivera den här funktionen efter körning i `prep` kommandot genom att uppdatera genererade Helm-diagrammet.

Den `prep` kommando inte ersätter eventuella befintliga Dockerfiles eller Helm diagram som du har i projektet. Om ett befintligt Dockerfile eller Helm-diagram använder samma namngivningskonvention som de filer som genererats av den `prep` kommandot, den `prep` kommandot hoppar över generera filerna. I annat fall den `prep` kommando genererar sin egen Dockerfile eller Helm-diagram längs sida de befintliga filerna.

Den `prep` kommando genererar även en `azds.yaml` i roten av projektet. Azure Dev blanksteg använder den här filen för att skapa, installera, konfigurera och kör ditt program. Den här konfigurationsfilen visas platsen för din Dockerfile och Helm-diagrammet och ger även ytterligare konfiguration utöver dessa artefakter.

Här är en exempelfil azds.yaml som skapats med hjälp av [.NET Core-exempelprogram](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

Den `azds.yaml` fil som skapats av den `prep` kommandot bör fungera bra för ett enda projekt med enkel utvecklingsscenario. Om ditt specifika projekt har ökat komplexiteten, kan du behöva uppdatera den här filen efter körning i `prep` kommando. Projektet kan exempelvis kräva visst mixtrande till din version eller starta process baserat på utveckling eller om du felsöker behov. Du kan också ha flera program i ditt projekt som kräver flera produktionsprocesser eller en annan build-innehåll.

## <a name="run-your-code"></a>Köra din kod

Om du vill köra din kod i dev kan utfärda de `up` i samma katalog som din `azds.yaml` fil:

```cmd
azds up
```

Den `up` kommando laddar upp programkällfilerna och andra artefakter som krävs för att skapa och köra projektet till området utveckling. Därifrån kan controller i dev-utrymme:

1. Skapar Kubernetes-objekt för att distribuera ditt program.
1. Skapar en behållare för ditt program.
1. Distribuerar programmet till området utveckling.
1. Skapar ett offentligt tillgänglig DNS-namn för programslutpunkten för ditt om konfigurerat.
1. Använder *port och tydlig* att ge åtkomst till appen slutpunkt med http://locahost.
1. Vidarebefordrar stdout- och stderr verktygsuppsättningen på klientsidan.


### <a name="starting-a-service"></a>Startar en tjänst

När du startar en tjänst i en dev-utrymme fungerar klientsidan verktyg och domänkontrollanten tillsammans att synkronisera källfilerna, skapa behållare och Kubernetes-objekten och kör ditt program.

På en mer detaljerad nivå, här är vad som händer när du kör `azds up`:

1. Filer synkroniseras från användarens dator till en Azure file storage som är unik för användarens AKS-kluster. Laddas källkoden-, Helm-diagram- och konfigurationsfiler. Mer information om synkroniseringsprocessen finns i nästa avsnitt.
1. Kontrollanten skapar en begäran om att starta en ny session. Den här begäran innehåller flera egenskaper, inklusive ett unikt ID, utrymme namnet, sökvägen till källkoden och en flagga för felsökning.
1. Domänkontrollanter ersätter den *$(tag)* platshållare i Helm-diagrammet med unikt sessions-ID och installerar Helm-diagram för din tjänst. Att lägga till en referens till unikt sessions-ID i Helm-diagrammet gör att behållaren distribueras till AKS-klustret för den här specifika sessionen kan kopplas till sessionen begäran och tillhörande information.
1. Lägger till ytterligare behållare i programpodden för instrumentation och åtkomst till ditt projekt källkod under installationen av Helm-diagrammet, Kubernetes webhook åtkomst server. Devspaces-proxy och devspaces proxy initiering behållare läggs för att tillhandahålla HTTP-spårning och utrymme routning. Behållaren devspaces-build läggs till för poden med åtkomst till Docker-instansen och projekt källkoden för att skapa dina programbehållare.
1. När programmets pod startas, används devspaces-build-behållaren och devspaces proxy initiering behållaren att skapa behållaren program. Programbehållare och devspaces proxy behållare startas sedan.
1. När behållaren program har startat funktionen på klientsidan används Kubernetes *port och tydlig* funktioner för att ge HTTP-åtkomst till ditt program via http://localhost. Den här portvidarebefordran ansluter utvecklingsdatorn till tjänsten i ditt dev-adressutrymme.
1. När alla behållare i en pod har startat kan körs tjänsten. Funktionen för klientsidan börjar nu att strömma HTTP-spårningar, stdout och stderr. Den här informationen visas som funktionen på klientsidan för utvecklare.

### <a name="updating-a-running-service"></a>Uppdatera en tjänst som körs

När en tjänst körs, har möjligheten att uppdatera tjänsten om någon av källfilerna projekt ändrar Azure Dev blanksteg. Dev blanksteg hanterar också uppdatera tjänsten på olika sätt beroende på vilken typ av filen som har ändrats. Det finns tre sätt Dev blanksteg kan uppdatera en pågående tjänst:

* Direkt uppdaterar en fil
* Återskapa och starta om programprocessen i behållare för de program som körs
* Bygga om och omdistribuera programmets behållare

![Azure Dev blanksteg-filsynkronisering](media/how-dev-spaces-works/file-sync.svg)

Vissa projektfiler som är statiska resurser, till exempel html, css och cshtml-filer, kan uppdateras direkt i programmets behållare utan att starta om vad som helst. Om en statisk tillgång ändras kan den nya filen synkroniseras till området utveckling och sedan används av behållaren som körs.

Ändringar i filer, till exempel källkoden eller konfigurationsfiler för programmet kan användas genom att starta om programprocessen inom behållaren som körs. När dessa filer är synkroniserade programmets processen startas i den behållaren som körs med den *devhostagent* process. När du först skapar programmets behållare, kontrollanten ersätter startkommandot för programmet med en annan process som kallas *devhostagent*. Den faktiska programprocessen körs som en underordnad process under *devhostagent*, och dess utdata skickas ut med *devhostagent*jobbs effekt. Den *devhostagent* processen ingår också i Dev blanksteg och kan köra kommandon i behållaren som körs åt utveckling blanksteg. När du utför en omstart *devhostagent*:

* Stoppar den aktuella processen eller processer som är kopplade till programmet
* Återskapar programmet
* Startar om processen eller processer som är kopplade till programmet

Hur *devhostagent* kör föregående stegen har konfigurerats i den `azds.yaml` konfigurationsfilen. Den här konfigurationen beskrivs i ett senare avsnitt.

Uppdateringar till projektfiler som Dockerfiles, csproj-filer eller någon del av Helm-diagrammet kräver programbehållare återskapas och omdistribueras. När någon av dessa filer synkroniseras till området dev kontrollanten körs den [uppgradering med helm](https://helm.sh/docs/helm/#helm-upgrade) kommandot och programmets behållare är återskapas och omdistribueras.

### <a name="file-synchronization"></a>Filsynkronisering

Första gången programmet startas i ett utrymme för utveckling, laddas alla appens källfiler. När programmet körs och på senare omstarter, laddas bara ändrade filer. Två filer som används för att samordna den här processen: en fil på klientsidan och en domänkontrollantsidan-fil.

Klientsidan filen lagras i en tillfällig katalog och heter baserat på hash för projektkatalogen körs i Dev blanksteg. På Windows skulle du till exempel har en fil som *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* för ditt projekt. På Linux, klientsidan filen lagras i den */tmp* directory. Du kan hitta katalogen på macOS genom att köra den `echo $TMPDIR` kommando.

Den här filen är i JSON-format och innehåller:

* En post för varje projektfil som är synkroniserad med dev-utrymme
* Ett ID för synkronisering
* Tidsstämpel för senaste synkroniseringsåtgärden

Varje fil projektposten innehåller en sökväg till filen och dess tidsstämpel.

Domänkontrollantsidan filen lagras på AKS-klustret. Det innehåller synkronisering-ID och tidsstämpel för den senaste synkroniseringen.

En synkronisering sker när synkronisering tidsstämplarna inte stämmer överens mellan klientsidan och domänkontrollantsidan-filer. Under en synkronisering itererar på klientsidan verktygsuppsättningen över filposter i filen på klientsidan. Om filens tidsstämpel är efter synkronisering tidsstämpel, synkroniseras filen till området utveckling. När synkroniseringen är klar, uppdateras synkronisering tidsstämplar på både klient- och domänkontrollantsidan filerna.

Alla projektfiler synkroniseras om klientside-filen inte finns. Det här beteendet kan du framtvinga en fullständig synkronisering genom att ta bort filen på klientsidan.

### <a name="how-routing-works"></a>Hur routning fungerar

Dev kan bygger på AKS och använder samma [nätverkskoncept](../aks/concepts-network.md). Azure Dev blanksteg har också en centraliserad *ingressmanager* -tjänsten och distribuerar en egen Ingress-kontrollant till AKS-klustret. Den *ingressmanager* Övervakare AKS-kluster med utveckling blanksteg och förstärker Azure Dev blanksteg ingående Controller i klustret med ingående objekt för routning till programmet poddar-tjänsten. Behållaren devspaces-proxy i varje pod lägger till en `azds-route-as` HTTP-huvud för HTTP-trafik till en dev-utrymme baserat på URL: en. Till exempel en begäran till URL: en *http://azureuser.s.default.serviceA.fedcba09...azds.io* får en HTTP-huvud med `azds-route-as: azureuser`. Behållaren devspaces proxy lägger inte till en `azds-route-as` rubriken om en sådan redan finns.

När en HTTP-begäran skickas till en tjänst från utanför klustret, går begäran till Ingress-kontrollant. Ingress-kontrollant dirigerar begäran direkt till rätt pod baserat på dess ingående objekt och regler. Behållaren devspaces-proxy i en pod tar emot begäran, lägger till den `azds-route-as` rubrik baserat på URL: en och sedan dirigerar begäran till behållaren för programmet.

När en HTTP-begäran skickas till en tjänst från en annan tjänst i klustret, går begäran först igenom den anropande service devspaces proxy-behållaren. Behållaren devspaces proxy tittar på HTTP-begäran och kontrollerar de `azds-route-as` rubrik. Baserat på rubriken, kollar behållaren devspaces proxy upp IP-adressen för tjänsten som är associerade med huvudets värde. Om en IP-adress hittas behållaren devspaces-proxy ska dras om begäran till IP-adress. Om en IP-adress inte hittas omdirigerar behållaren devspaces proxy begäran till den överordnade behållaren för programmet.

Till exempel program *serviceA* och *serviceB* distribueras till en överordnad dev utrymme som kallas *standard*. *serviceA* förlitar sig på *serviceB* och gör HTTP-anrop till den. Azure-användare skapar en underordnad dev utrymme baserat på den *standard* utrymme som kallas *azureuser*. Azure-användare distribuerar också sin egen version av *serviceA* till sina underordnade utrymme. När en begäran skickas till *http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Azure Dev blanksteg Routning](media/how-dev-spaces-works/routing.svg)

1. Ingress-kontrollant letar upp den IP-Adressen för din pod som är associerade med den URL som är *serviceA.azureuser*.
1. Ingress-kontrollanten söker efter IP-Adressen för din pod i utrymmet för utveckling av Azure-användare och dirigerar begäran till den *serviceA.azureuser* pod.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod får begäran och lägger till `azds-route-as: azureuser` som ett HTTP-huvud.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod dirigerar begäran till den *serviceA* programbehållare i den *serviceA.azureuser* pod.
1. Den *serviceA* program i den *serviceA.azureuser* pod anropar *serviceB*. Den *serviceA* programmet även innehåller kod för att bevara den befintliga `azds-route-as` rubriken, som i det här fallet `azds-route-as: azureuser`.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod tar emot begäran och letar upp IP-Adressen för *serviceB* baserat på värdet för den `azds-route-as` rubrik.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod inte hitta en IP-adress för *serviceB.azureuser*.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod letar upp den IP-Adressen för *serviceB* i det överordnade utrymmet, vilket är *serviceB.default*.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod hittar du IP för *serviceB.default* och dirigerar begäran till den *serviceB.default* pod.
1. Behållaren devspaces-proxy i den *serviceB.default* pod tar emot begäran och dirigerar begäran till den *serviceB* programbehållare i den *serviceB.default*pod.
1. Den *serviceB* program i den *serviceB.default* pod returnerar ett svar till den *serviceA.azureuser* pod.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod tar emot svaret och skickar svaret på den *serviceA* programbehållare i den *serviceA.azureuser* pod.
1. Den *serviceA* program tar emot svaret och returnerar sedan sitt eget svar.
1. Behållaren devspaces-proxy i den *serviceA.azureuser* pod tar emot svaret från den *serviceA* programbehållare och skickar svaret till ursprungliga anroparen utanför klustret.

Alla andra TCP-trafik som inte är HTTP passerar Ingress-kontrollanten och devspaces-proxy-behållare som ska ändras.

### <a name="how-running-your-code-is-configured"></a>Hur kör koden har konfigurerats

Azure Dev blanksteg använder den `azds.yaml` filen för att installera och konfigurera din tjänst. Domänkontrollanten använder den `install` -egenskapen i den `azds.yaml` filen för att installera Helm-diagrammet och skapa Kubernetes-objekten:

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

Som standard den `prep` kommando genererar Helm-diagrammet. I exemplet anges också den *install.chart* egenskapen till katalogen för Helm-diagrammet. Om du vill använda ett Helm-diagram i en annan plats, kan du uppdatera den här egenskapen om du vill använda den platsen.

När du installerar Helm-diagram, kan Azure Dev blanksteg åsidosätta värden i Helm-diagrammet. Standardvärden för Helm-diagrammet finns i `charts/APP_NAME/values.yaml`.

Med hjälp av den *install.values* egenskapen, som du kan ange en eller flera filer som definierar värden som du vill ha ersatta i Helm-diagrammet. Du kan till exempel använda den här åsidosättningsfunktionerna om du vill ha en konfiguration för värdnamn eller databasen särskilt när du kör programmet i en dev-utrymmet. Du kan också lägga till en *?* i slutet av någon av filnamn för att ange den som valfria.

Den *install.set* egenskapen kan du konfigurera ett eller flera värden som du vill ha ersatta i Helm-diagrammet. Alla värden som konfigurerats i *install.set* åsidosätter värden som konfigurerats i filer som anges i *install.values*. I egenskaperna under *install.set* är beroende av värdena i Helm-diagrammet och kan skilja sig beroende på genererade Helm-diagrammet.

I exemplet ovan den *install.set.replicaCount* egenskapen talar om för kontrollanten hur många instanser av programmet att köras i ditt dev-adressutrymme. Du kan öka det här värdet beroende på ditt scenario, men den har en inverkan på Koppla en felsökare till din programpodden. Mer information finns i den [felsökningsartikeln](troubleshooting.md).

I den genererade Helm-diagrammet behållaravbildningen anges till *{{. Values.Image.Repository}} :{{. Values.Image.tag}}*. Den `azds.yaml` fil definierar *install.set.image.tag* egenskapen som *$(tag)* som standard som används som värde för *{{. Values.Image.tag}}*. Genom att ange den *install.set.image.tag* egenskapen i det här sättet tillåter behållaravbildningen för ditt program taggas på olika sätt när du kör Azure Dev blanksteg. I det specifika fallet, avbildningen har märkts som  *<value from image.repository>: $(tag)*. Du måste använda den *$(tag)* variabeln som värde för *install.set.image.tag* för Dev blanksteg känner igen och leta rätt på behållaren i AKS-kluster.

I exemplet ovan `azds.yaml` definierar *install.set.ingress.hosts*. Den *install.set.ingress.hosts* egenskapen definierar en värdnamnsformat för offentliga slutpunkter. Den här egenskapen används också *$(spacePrefix)*, *$(rootSpacePrefix)*, och *$(hostSuffix)*, vilket är värden som tillhandahålls av kontrollanten. 

Den *$(spacePrefix)* är namnet på det underordnade dev utrymmet, som i form av *SPACENAME.s*. Den *$(rootSpacePrefix)* är namnet på det överordnade utrymmet. Till exempel om *azureuser* är en underordnad plats för *standard*, värdet för *$(rootSpacePrefix)* är *standard* och värdet för *$(spacePrefix)* är *azureuser.s*. Om utrymme inte är en underordnad utrymme *$(spacePrefix)* är tomt. Till exempel om den *standard* utrymme har överordnade utrymme, värdet för *$(rootSpacePrefix)* är *standard* och värdet för *$(spacePrefix)* är tomt. Den *$(hostSuffix)* är ett DNS-suffix som pekar på Azure Dev blanksteg ingående Controller som körs i AKS-klustret. Den här DNS-suffix motsvarar en DNS-post för jokertecken, till exempel  *\*. RANDOM_VALUE.EUs.azds.IO*, som skapades när Azure Dev blanksteg kontrollanten har lagts till i AKS-klustret.

I ovanstående `azds.yaml` fil, du kan också uppdatera *install.set.ingress.hosts* ändra värdnamnet för ditt program. Exempel: Om du vill förenkla värdnamnet för ditt program från *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* till *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Om du vill skapa en behållare för ditt program, kontrollanten använder den nedan delar av den `azds.yaml` konfigurationsfil:

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

Kontrollanten använder en Dockerfile för att skapa och köra dina program.

Den *build.context* egenskapslistor katalogen där Dockerfiles finns. Den *build.dockerfile* egenskapen definierar namnet på Dockerfile för att skapa produktionsversionen av programmet. Den *configurations.develop.build.dockerfile* egenskapen konfigurerar namnet på Dockerfile för utveckling-versionen av programmet.

Med olika Dockerfiles för utveckling och produktion kan du aktivera vissa saker under utvecklingen och inaktivera objekten för Produktionsdistribution. Du kan till exempel aktivera felsökning eller mer utförlig loggning under utveckling och inaktivera i en produktionsmiljö. Du kan också uppdatera de här egenskaperna om din Dockerfiles namnges på olika sätt eller i en annan plats.

Så att du snabbt kan iterera under utvecklingen, kommer Azure Dev blanksteg synkronisera ändringar från ditt lokala projekt och stegvis uppdatera ditt program. Den under avsnitt i den `azds.yaml` konfigurationsfilen används för att konfigurera synkroniseringen och uppdatera:

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

Filer och kataloger som kommer att synkronisera ändringar visas i den *configurations.develop.container.sync* egenskapen. Dessa kataloger synkroniseras först när du kör den `up` kommandot samt när ändringar identifieras. Om det finns ytterligare eller andra kataloger som synkroniseras till din dev-sida kan ändra du den här egenskapen.

Den *configurations.develop.container.iterate.buildCommands* egenskap anger hur du skapar program i ett utvecklingsscenario för. Den *configurations.develop.container.command* egenskapen tillhandahåller kommandot för att köra programmet i ett utvecklingsscenario för. Du kanske vill uppdatera för dessa egenskaper om det finns ytterligare flaggor för bygge eller runtime eller parametrar som du vill använda under utveckling.

Den *configurations.develop.container.iterate.processesToKill* visar en lista över processerna för att avsluta om du vill stoppa programmet. Du kanske vill uppdatera den här egenskapen om du vill ändra beteendet för omstart av ditt program under utveckling. Exempel: Om du uppdaterat den *configurations.develop.container.iterate.buildCommands* eller *configurations.develop.container.command* egenskaper för att ändra hur du har skapat programmet eller igång kan behöva du ändra vilka processer har stoppats.

När du förbereder din kod med hjälp av den `azds prep` kommandot, har du möjlighet att lägga till den `--public` flaggan. Att lägga till den `--public` flaggan skapar en offentligt tillgänglig URL för ditt program. Om du utelämnar den här flaggan är programmet endast åtkomlig inom klustret eller med hjälp av localhost-tunnel. När du har kört den `azds prep` kommandot, du kan ändra den här inställningen ändrar den *ingress.enabled* -egenskapen i `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Felsöka kod

Du kan felsöka ditt program som körs direkt i ditt dev-adressutrymmet med hjälp av Visual Studio Code eller Visual Studio 2017 för Java, .NET och Node.js-program. Visual Studio Code och Visual Studio 2017 ger verktyg för att ansluta till ditt dev adressutrymme, starta programmet och koppla en felsökare. Efter körning `azds prep`, kan du öppna projektet i Visual Studio Code eller Visual Studio 2017. Visual Studio Code eller Visual Studio 2017 genererar sina egna konfigurationsfiler för att ansluta som skiljer sig från att köras `azds prep`. Från inom Visual Studio Code eller Visual Studio 2017, kan du ange brytpunkter och starta programmet ditt dev-adressutrymme.

![Felsöka din kod](media/get-started-node/debug-configuration-nodejs2.png)

När du startar ditt program med Visual Studio Code eller Visual Studio 2017 för felsökning, de hanterar starta och ansluta till ditt dev-utrymme på samma sätt som att köra `azds up`. Klientsidan verktygsuppsättningen i Visual Studio Code och Visual Studio 2017 tillhandahåller även en extra parameter med specifik information för felsökning. Parametern innehåller namnet på felsökare avbildning, platsen för felsökningsprogrammet i felsökning bild och målplatsen i programmets behållaren att montera mappen felsökare. 

Felsökare avbildningen bestäms automatiskt av verktyg för klientsidan. Den använder en metod som liknar den som används under Dockerfile och Helm-diagrammet generera när du kör `azds prep`. När felsökningen är monterad i programmets bild, körs det med hjälp av `azds exec`.

## <a name="sharing-a-dev-space"></a>Dela en dev-utrymme

När du arbetar med ett team, kan du [utveckling kan delas av en hela arbetsgruppen](how-to/share-dev-spaces.md) och skapa härledda dev blanksteg. Dev kan användas för alla med deltagarbehörighet inom området dev resursgrupp.

Du kan också skapa ett nytt dev-utrymme som härleds från en annan dev utrymme. När du skapar en härledd dev-utrymme i *azds.io/överordnad-utrymme = ÖVERORDNADE-utrymme-NAME* etiketten läggs till i området härledda dev-namnområdet. Dessutom delas alla program från det överordnade dev utrymmet med härledda dev utrymme. Om du distribuerar en uppdaterad version av ett program till området härledda utveckling, finns det bara i området härledda utveckling och överordnade dev utrymme förblir påverkas inte. Du kan ha högst tre nivåer av härledda dev blanksteg eller *överordnade* blanksteg.

Området härledda dev dirigerar också smart begäranden mellan egna program och de program som delas från dess överordnade objekt. Routning fungerar genom att försök att dirigera förfrågan till ett program i området härledda utveckling och återgång till delade programmet från det överordnade dev utrymmet. Routning tillbaka till det delade programmet i det överordnade utrymmet om programmet inte är i det överordnade utrymmet.

Exempel:
* Dev-utrymme *standard* har program *serviceA* och *serviceB* .
* Dev-utrymme *azureuser* härleds från *standard*.
* En uppdaterad version av *serviceA* har distribuerats till *azureuser*.

När du använder *azureuser*, alla förfrågningar till *serviceA* kommer att dirigeras till den uppdaterade versionen i *azureuser*. En begäran om att *serviceB* försöker först använda ska vidarebefordras till den *azureuser* version av *serviceB*. Eftersom det inte finns, kommer den att dirigeras till den *standard* version av *serviceB*. Om den *azureuser* version av *serviceA* tas bort, alla förfrågningar till *serviceA* kommer att gå med i *standard* version av *serviceA*.

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Dev blanksteg, se följande snabbstarter:

* [Java med CLI och Visual Studio Code](quickstart-java.md)
* [.NET core med CLI och Visual Studio Code](quickstart-netcore.md)
* [.NET core med Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js med CLI och Visual Studio Code](quickstart-nodejs.md)

Kom igång med utveckling i grupp, se följande instruktionsartiklar:

* [Grupputveckling – Java med CLI och Visual Studio Code](team-development-java.md)
* [Grupputveckling - .NET Core med CLI och Visual Studio Code](team-development-netcore.md)
* [Grupputveckling - .NET Core med Visual Studio 2017](team-development-netcore-visualstudio.md)
* [Grupputveckling – Node.js med CLI och Visual Studio Code](team-development-nodejs.md)
