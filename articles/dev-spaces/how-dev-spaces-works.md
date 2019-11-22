---
title: Så här fungerar Azure dev Spaces och konfigureras
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Beskriver de processer som Power Spaces för Azure dev och hur de konfigureras i konfigurations filen azds. yaml
keywords: azds. yaml, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 9efae0e9d6bc53e08dce604fa79aa29e158ecabd
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280135"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Så här fungerar Azure dev Spaces och konfigureras

Det kan vara svårt att utveckla ett Kubernetes-program. Du behöver Docker-och Kubernetes-konfigurationsfiler. Du måste ta reda på hur du testar programmet lokalt och interagerar med andra beroende tjänster. Du kan behöva hantera utveckling och testning på flera tjänster samtidigt och med en grupp utvecklare.

Med Azure dev Spaces kan du utveckla, distribuera och felsöka Kubernetes-program direkt i Azure Kubernetes service (AKS). Azure dev Spaces gör det också möjligt för ett team att dela ett dev-utrymme. Genom att dela ett dev-utrymme i ett team kan enskilda grupp medlemmar utveckla i isolering utan att behöva replikera eller modellera beroenden eller andra program i klustret.

Azure dev Spaces skapar och använder en konfigurations fil för att distribuera, köra och felsöka dina Kubernetes-program i AKS. Den här konfigurations filen finns i program koden och kan läggas till i versions kontroll systemet.

Den här artikeln beskriver de processer som Power Spaces i Azure dev och hur dessa processer konfigureras i konfigurations filen för Azure dev Spaces. För att få Azure dev-utrymmen igång snabbt och se det i praktiken, slutför en av snabb starterna:

* [Java med CLI och Visual Studio Code](quickstart-java.md)
* [.NET Core med CLI och Visual Studio Code](quickstart-netcore.md)
* [.NET Core med Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js med CLI och Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Så här fungerar Azure Dev Spaces

Azure dev Spaces har två distinkta komponenter som du interagerar med: kontroll enheten och verktyg på klient sidan.

![Azure dev Spaces-komponenter](media/how-dev-spaces-works/components.svg)

Kontrollanten utför följande åtgärder:

* Hanterar skapande och val av dev-utrymme.
* Installerar programmets Helm-diagram och skapar Kubernetes-objekt.
* Skapar programmets behållar avbildning.
* Distribuerar ditt program till AKS.
* Skapar stegvisa versioner och startar om när käll koden ändras.
* Hanterar loggar och HTTP-spår.
* Vidarebefordrar STDOUT och stderr till verktyg på klient sidan.
* Tillåter att grupp medlemmar skapar underordnade dev-rymder som härletts från ett överordnat dev-utrymme.
* Konfigurerar routning för program inom ett utrymme samt över över-och underordnade utrymmen.

Kontrollanten finns utanför AKS. Det styr beteende och kommunikation mellan klient sidans verktyg och AKS-klustret. Kontrollanten aktive ras med Azure CLI när du förbereder klustret för att använda Azure dev Spaces. När den är aktive rad kan du interagera med den med hjälp av verktyg på klient sidan.

Med verktyg på klient sidan kan användaren:
* Skapa ett Dockerfile-, Helm-diagram och konfigurations filen för Azure dev Spaces för programmet.
* Skapa överordnade och underordnade dev-sidor.
* Be kontrollanten att skapa och starta ditt program.

När ditt program körs, kan verktyg på klient sidan också:
* Tar emot och visar STDOUT och stderr från ditt program som körs i AKS.
* Använder [Port-Forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) för att tillåta webb åtkomst till ditt program med http:\//localhost.
* Bifogar en fel sökare till det program som körs i AKS.
* Synkroniserar käll koden till ditt dev-utrymme när en ändring identifieras för stegvisa versioner, vilket möjliggör snabb iteration.

Du kan använda klient sidans verktyg från kommando raden som en del av kommandot `azds`. Du kan också använda verktyget på klient sidan med:

* Visual Studio Code med [tillägget Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio med [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools).

Här är det grundläggande flödet för att konfigurera och använda Azure dev Spaces:
1. Förbereda ditt AKS-kluster för Azure dev Spaces
1. Förbered din kod för att köras i Azure dev Spaces
1. Kör din kod på ett dev-utrymme
1. Felsöka din kod i ett dev-utrymme
1. Dela ett dev-utrymme

Vi kommer att få mer information om hur Azure dev Spaces fungerar i vart och ett av avsnitten nedan.

## <a name="prepare-your-aks-cluster"></a>Förbereda ditt AKS-kluster

Att förbereda ditt AKS-kluster omfattar:
* Att verifiera ditt AKS-kluster finns i en region [som stöds av Azure dev Spaces][supported-regions].
* Verifierar att du kör Kubernetes 1.10.3 eller senare.
* Aktivera Azure dev Spaces i klustret med hjälp av `az aks use-dev-spaces`

Mer information om hur du skapar och konfigurerar ett AKS-kluster för Azure dev Spaces finns i en av komma igång-guiderna:
* [Kom igång med Azure dev Spaces med Java](get-started-java.md)
* [Kom igång med Azure dev Spaces med .NET Core och Visual Studio](get-started-netcore-visualstudio.md)
* [Kom igång med Azure dev Spaces med .NET Core](get-started-netcore.md)
* [Kom igång med Azure dev Spaces med Node. js](get-started-nodejs.md)

När du har aktiverat Azure dev Spaces på ditt AKS-kluster installeras kontrollanten för klustret. Kontrollanten är en separat Azure-resurs utanför klustret och gör följande för resurser i klustret:

* Skapar eller anger ett Kubernetes-namnområde som ska användas som ett dev-utrymme.
* Tar bort alla Kubernetes-namnområden med namnet *azds*, om det finns, och skapar ett nytt.
* Distribuerar en Kubernetes webhook-konfiguration.
* Distribuerar en server för webhook-åtkomstkontroll.
    

Den använder också samma tjänst huvud namn som ditt AKS-kluster använder för att skapa tjänst anrop till andra Azure dev Spaces-komponenter.

![Azure dev Spaces prepare-kluster](media/how-dev-spaces-works/prepare-cluster.svg)

För att kunna använda Azure dev Spaces måste det finnas minst ett dev-utrymme. I Azure dev Spaces används Kubernetes-namnområden i ditt AKS-kluster för dev Spaces. När en kontrollant installeras, blir du ombedd att skapa ett nytt Kubernetes-namnområde eller välja en befintlig namnrymd som ska användas som ditt första utvecklings utrymme. När ett namn område anges som ett dev-utrymme lägger kontrollanten till *azds.io/Space=True* -etiketten i namn området för att identifiera den som ett dev-utrymme. Det inledande dev-utrymmet som du skapar eller anger är markerat som standard när du förbereder klustret. När ett blank steg är markerat används det av Azure dev Spaces för att skapa nya arbets belastningar.

Som standard skapar kontrollanten ett dev-utrymme med namnet *default* genom att uppgradera det befintliga *standard* namn området Kubernetes. Du kan använda klient sidans verktyg för att skapa nya dev-Spaces och ta bort befintliga dev-sidor. Det går inte att ta bort *standard* dev-utrymmet på grund av en begränsning i Kubernetes. Kontrollanten tar också bort alla befintliga Kubernetes-namnområden med namnet *azds* för att undvika konflikter med kommandot `azds` som används av klient sidans verktyg.

Kubernetes webhook-åtkomstkontroll används för att injicera poddar med tre behållare under distribution för instrumentering: en devspaces-proxy-behållare, en devspaces-proxy-init-behållare och en devspaces-build-behållare. **Alla tre dessa behållare körs med rot åtkomst i ditt AKS-kluster.** De använder också samma tjänst huvud namn som ditt AKS-kluster använder för att skapa tjänst anrop till andra Azure dev Spaces-komponenter.

![Azure dev Spaces Kubernetes webhook Admission Server](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy-behållaren är en sidvagn-behållare som hanterar all TCP-trafik till och från program behållaren och gör det möjligt att utföra routning. Devspaces-proxyn dirigerar om HTTP-meddelanden om vissa blank steg används. Det kan till exempel hjälpa att dirigera HTTP-meddelanden mellan program i överordnade och underordnade utrymmen. All icke-HTTP-trafik passerar genom devspaces-proxy oförändrad. Devspaces-proxy-behållaren loggar också alla inkommande och utgående HTTP-meddelanden och skickar dem till klient sidans verktyg som spår. Dessa spår kan sedan visas av utvecklaren för att kontrol lera programmets beteende.

Devspaces-proxy-init-behållaren är en [init-behållare](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) som lägger till ytterligare regler för routning baserat på utrymmes-hierarkin till programmets behållare. Den lägger till regler för routning genom att uppdatera program behållarens */etc/resolv.conf* -fil och program varan iptables-konfiguration innan den startas. Uppdateringarna av */etc/resolv.conf* tillåter DNS-matchning av tjänster i överordnade utrymmen. Konfigurations uppdateringarna för program varan iptables säkerställer att all TCP-trafik till och från programmets behållare dirigeras även om devspaces-proxy. Alla uppdateringar från devspaces-proxy-init sker utöver de regler som Kubernetes lägger till.

Devspaces-build-behållaren är en init-behållare och har projektets käll kod och Docker-socket monterad. Projekt käll koden och åtkomst till Docker gör att program behållaren kan skapas direkt av pod.

> [!NOTE]
> Azure dev Spaces använder samma nod för att skapa programmets behållare och köra den. Det innebär att Azure dev Spaces inte behöver ett externt behållar register för att skapa och köra ditt program.

Kubernetes webhook-åtkomstkontroll lyssnar efter nya Pod som skapats i AKS-klustret. Om Pod har distribuerats till ett namn område med *azds.io/Space=True* -etiketten injiceras Pod med ytterligare behållare. Devspaces-build-behållaren matas bara in om programmets behållare körs med klient sidans verktyg.

När du har för berett ditt AKS-kluster kan du använda verktyg på klient sidan för att förbereda och köra din kod i ditt utvecklings utrymme.

## <a name="prepare-your-code"></a>Förbered din kod

För att kunna köra programmet i ett dev-utrymme måste den vara containerd och du måste definiera hur det ska distribueras till Kubernetes. För att Använd ditt program behöver du en Dockerfile. Om du vill definiera hur programmet distribueras till Kubernetes behöver du ett [Helm-diagram](https://docs.helm.sh/). För att hjälpa till med att skapa både Dockerfile-och Helm-diagrammet för ditt program, innehåller verktyg på klient sidan kommandot `prep`:

```cmd
azds prep --public
```

Kommandot `prep` kommer att titta på filerna i projektet och försöka skapa Dockerfile-och Helm-diagrammet för att köra programmet i Kubernetes. För närvarande genererar `prep` kommandot ett Dockerfile-och Helm-diagram med följande språk:

* Java
* Node.js
* .NET Core

Du *måste* köra kommandot `prep` från en katalog som innehåller käll koden. Genom att köra kommandot `prep` från rätt katalog kan verktyg på klient sidan identifiera språket och skapa en lämplig Dockerfile för att Använd ditt program. Du kan också köra kommandot `prep` från en katalog som innehåller en *Pom. XML-* fil för Java-projekt.

Om du kör kommandot `prep` från katalog som inte innehåller någon käll kod genererar klient sidans verktyg inte en Dockerfile. Det visar också ett fel som säger: *Dockerfile kunde inte genereras på grund*av ett språk som inte stöds. Det här felet uppstår även om verktyg på klient sidan inte känner igen projekt typen.

När du kör kommandot `prep` har du möjlighet att ange `--public`-flaggan. Den här flaggan anger att kontrollanten ska skapa en tillgänglig slut punkt för Internet för den här tjänsten. Om du inte anger den här flaggan är tjänsten bara tillgänglig i klustret eller med hjälp av den localhost-tunnel som skapats av klient sidans verktyg. Du kan aktivera eller inaktivera det här beteendet när du har kört kommandot `prep` genom att uppdatera det genererade Helm-diagrammet.

Kommandot `prep` ersätter inte befintliga Dockerfiles-eller Helm-diagram som du har i projektet. Om ett befintligt Dockerfile-eller Helm-diagram använder samma namngivnings konvention som de filer som genereras av kommandot `prep` hoppar kommandot `prep` över genereringen av filerna. Annars genererar `prep` kommandot ett eget Dockerfile-eller Helm-diagram längs de befintliga filerna.

Kommandot `prep` kommer också att generera en `azds.yaml`-fil i projektets rot. Azure dev Spaces använder den här filen för att skapa, installera, konfigurera och köra ditt program. Den här konfigurations filen visar platsen för ditt Dockerfile-och Helm-diagram och ger även ytterligare konfiguration ovanpå dessa artefakter.

Här är ett exempel på en azds. yaml-fil som skapats med [.net Core-exempel program](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

Den `azds.yaml`-fil som genereras av `prep` kommandot bör fungera bra för ett enkelt, enda projekt utvecklings scenario. Om det aktuella projektet har ökad komplexitet kan du behöva uppdatera filen när du har kört kommandot `prep`. Ditt projekt kan till exempel kräva att du anpassar din build-eller start process baserat på dina utvecklings-eller fel söknings behov. Du kan också ha flera program i projektet, vilket kräver flera Bygg processer eller ett annat build-innehåll.

## <a name="run-your-code"></a>Kör din kod

Om du vill köra din kod i ett dev-utrymme utfärdar du kommandot `up` i samma katalog som `azds.yaml`s filen:

```cmd
azds up
```

Kommandot `up` laddar upp dina programkällfiler och andra artefakter som behövs för att skapa och köra ditt projekt i dev-området. Därifrån i ditt dev Space:

1. Skapar Kubernetes-objekt för att distribuera programmet.
1. Skapar behållaren för ditt program.
1. Distribuerar ditt program till dev-utrymmet.
1. Skapar ett offentligt tillgängligt DNS-namn för program slut punkten om det är konfigurerat.
1. Använder *Port-Forward* för att ge åtkomst till program slut punkten med hjälp av http://localhost.
1. Vidarebefordrar STDOUT och stderr till verktyg på klient sidan.


### <a name="starting-a-service"></a>Starta en tjänst

När du startar en tjänst i ett dev-utrymme fungerar klient sidans verktyg och kontrollant i samordning för att synkronisera källfilerna, skapa din behållare och Kubernetes-objekt och köra ditt program.

På en mer detaljerad nivå är det vad som händer när du kör `azds up`:

1. Filerna synkroniseras från användarens dator till en Azure File Storage som är unik för användarens AKS-kluster. Käll koden, Helm-diagrammet och konfigurationsfilerna laddas upp. Mer information om synkroniseringsprocessen finns i nästa avsnitt.
1. Kontrollanten skapar en begäran om att starta en ny session. Den här begäran innehåller flera egenskaper, inklusive ett unikt ID, utrymmes namn, sökväg till käll koden och en fel söknings flagga.
1. Kontrollanten ersätter plats hållaren *$ (tag)* i Helm-diagrammet med det unika sessions-ID: t och installerar Helm-diagrammet för din tjänst. Genom att lägga till en referens till det unika sessions-ID: t i Helm-diagrammet kan behållaren som distribueras till AKS-klustret för den här specifika sessionen kopplas tillbaka till sessionen och den tillhör ande informationen.
1. Under installationen av Helm-diagrammet lägger Kubernetes webhook-åtkomstkontroll till ytterligare behållare till programmets Pod för instrumentering och åtkomst till projektets käll kod. Devspaces-proxy-och devspaces-proxy-init-behållare läggs till för att tillhandahålla HTTP-spårning och utrymmes dirigering. Devspaces-build-behållaren läggs till för att ge Pod åtkomst till Docker-instansen och projekt käll koden för att skapa din program behållare.
1. När programmets Pod startas, används devspaces-build-behållaren och devspaces-proxy-init-behållaren för att bygga program behållaren. Program behållaren och devspaces-behållare startas sedan.
1. När program behållaren har startat använder klient sidan funktionen Kubernetes *-Port-Forward* för att ge http-åtkomst till ditt program över http://localhost. Den här port vidarebefordringen ansluter din utvecklings dator till tjänsten i ditt utvecklings utrymme.
1. När alla behållare i pod har startat körs tjänsten. I det här läget börjar funktionen på klient sidan att strömma HTTP-spåren, STDOUT och stderr. Den här informationen visas i klient sidans funktioner för utvecklaren.

### <a name="updating-a-running-service"></a>Uppdatera en aktiv tjänst

När en tjänst körs har Azure dev Spaces möjlighet att uppdatera tjänsten om någon av Project-källfilerna ändras. Dev Spaces hanterar också uppdatering av tjänsten på olika sätt beroende på vilken typ av fil som ändras. Det finns tre sätt att använda dev Spaces för att uppdatera en tjänst som körs:

* Uppdatera en fil direkt
* Återskapa och starta om programmets process i den program behållare som körs
* Återskapa och omdistribuerar programmets behållare

![Fil synkronisering för Azure dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Vissa projektfiler som är statiska till gångar, till exempel HTML-, CSS-och cshtml-filer, kan uppdateras direkt i programmets behållare utan att du behöver starta om något. Om en statisk till gång ändras, synkroniseras den nya filen med dev-utrymmet och används sedan av behållaren som körs.

Ändringar i filer som källkod eller programkonfigurationsfiler kan tillämpas genom att starta om programmets process inom den pågående behållaren. När de här filerna har synkroniserats startas programmets process om inom den behållare som körs med hjälp av processen *devhostagent* . När du skapar programmets behållare i första hand, ersätter kontrollanten Start kommandot för programmet med en annan process som heter *devhostagent*. Programmets faktiska process körs sedan som en underordnad process under *devhostagent*och dess utdata är skickas ut med *devhostagent*utdata. Processen *devhostagent* är också en del av dev Spaces och kan köra kommandon i den behållare som körs på uppdrag av dev Spaces. När du utför en omstart, *devhostagent*:

* Stoppar den aktuella processen eller de processer som är associerade med programmet
* Återskapar programmet
* Startar om processen eller processerna som är associerade med programmet

Hur *devhostagent* kör föregående steg konfigureras i `azds.yaml` konfigurations filen. Den här konfigurationen beskrivs i ett senare avsnitt.

Uppdateringar av projektfiler som Dockerfiles, CSPROJ-filer eller någon del av Helm-diagrammet kräver att programmets behållare återskapas och distribueras om. När en av dessa filer synkroniseras till dev-utrymmet kör kontrollanten [Helm Upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) -kommandot och programmets behållare återskapas och distribueras om.

### <a name="file-synchronization"></a>Filsynkronisering

Första gången ett program startas i ett utvecklings utrymme laddas alla källfiler för programmet upp. När programmet körs och senare startar om, laddas bara de ändrade filerna upp. Två filer används för att samordna processen: en fil på klient sidan och en fil på styrenhets sidan.

Filen på klient sidan lagras i en tillfällig katalog och namnges baserat på en hash för projekt katalogen som du kör i dev Spaces. I Windows skulle du till exempel ha en fil som *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* för ditt projekt. På Linux lagras filen på klient sidan i *katalogen/tmp* -katalogen. Du kan hitta katalogen på macOS genom att köra kommandot `echo $TMPDIR`.

Den här filen är i JSON-format och innehåller:

* En post för varje projekt fil som synkroniseras med dev-utrymmet
* Ett synkroniserings-ID
* Tidsstämpel för den senaste synkroniseringsåtgärden

Varje projekt fils post innehåller en sökväg till filen och dess tidsstämpel.

Enhets sidans fil lagras i AKS-klustret. Den innehåller synkroniserings-ID och tidsstämpel för den senaste synkroniseringen.

En synkronisering inträffar när tidsstämplar för synkronisering inte matchar mellan klient sidan och styrenhets sidans filer. Under en synkronisering upprepas klient sidans verktyg över fil posterna i filen på klient sidan. Om filens tidstämpel är efter synkroniseringens tidsstämpel, synkroniseras filen med dev-ytan. När synkroniseringen är klar uppdateras synkroniseringens tidsstämplar både på klient sidan och på styrenhets sidan.

Alla projektfiler synkroniseras om filen på klient sidan inte finns. Med det här beteendet kan du framtvinga en fullständig synkronisering genom att ta bort filen på klient sidan.

### <a name="how-routing-works"></a>Så här fungerar routning

Ett dev-utrymme bygger på AKS och använder samma [nätverks koncept](../aks/concepts-network.md). Azure dev Spaces har också en centraliserad *ingressmanager* -tjänst och distribuerar sin egen ingångs kontroll till AKS-klustret. *Ingressmanager* -tjänsten övervakar AKS-kluster med dev Spaces och ökar den ingångs kontroll för Azure dev-platser i klustret med ingångs objekt för routning till program poddar. Devspaces-proxy-behållaren i varje Pod lägger till ett `azds-route-as` HTTP-huvud för HTTP-trafik till ett dev-utrymme baserat på URL: en. En begäran till URL: en *http://azureuser.s.default.serviceA.fedcba09...azds.io* skulle till exempel få ett HTTP-huvud med `azds-route-as: azureuser`. Devspaces-proxyn kommer inte att lägga till ett `azds-route-as`-huvud om det redan finns en.

När en HTTP-begäran görs till en tjänst utanför klustret, skickas begäran till ingångs styrenheten. Ingångs styrenheten dirigerar begäran direkt till lämplig Pod baserat på dess ingångs objekt och regler. Devspaces-proxy-behållaren i pod tar emot begäran, lägger till `azds-route-as`-sidhuvudet baserat på URL: en och skickar sedan begäran till program behållaren.

När en HTTP-begäran görs till en tjänst från en annan tjänst i klustret går begäran först igenom den anropande tjänstens devspaces-proxy-behållare. Devspaces-proxy-behållaren tittar på HTTP-begäran och kontrollerar `azds-route-as`s huvudet. Baserat på rubriken letar devspaces-proxy-behållaren upp IP-adressen för tjänsten som är associerad med huvudets värde. Om det finns en IP-adress dirigerar devspaces-proxyn om begäran till den IP-adressen. Om det inte går att hitta en IP-adress dirigerar devspaces-behållaren begäran till den överordnade program behållaren.

Till exempel distribueras programmen *service* och *serviceB* till ett överordnat dev-utrymme som kallas *default*. *serva* förlitar sig på *serviceB* och gör HTTP-anrop till den. Azure User skapar ett underordnat dev-utrymme baserat på *standard* utrymmet som kallas *azureuser*. Azure-användare distribuerar också sin egen version av *servicen* till deras underordnade utrymme. När en begäran görs till *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Azure dev Spaces-routning](media/how-dev-spaces-works/routing.svg)

1. Ingångs styrenheten söker efter IP-adressen för Pod som är associerad med URL: en, som är *service. azureuser*.
1. Ingångs styrenheten hittar IP-adressen för Pod i Azure-användarens dev-utrymme och dirigerar begäran till *tjänsten. azureuser* pod.
1. Devspaces-proxy-behållaren i *tjänsten service-azureuser* Pod tar emot begäran och lägger till `azds-route-as: azureuser` som ett HTTP-huvud.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod dirigerar begäran till *tjänsten service-* program i *tjänst-azureuser-* pod.
1. *Tjänsten service-* i *-azureuser* Pod gör ett anrop till *serviceB*. *Tjänsten service-* app innehåller också kod för att bevara det befintliga `azds-route-as`s huvudet, som i det här fallet är `azds-route-as: azureuser`.
1. Devspaces-proxy-behållaren i *tjänsten service-azureuser* Pod tar emot begäran och letar upp IP-adressen för *serviceB* baserat på värdet för `azds-route-as`s huvudet.
1. Devspaces-proxy-behållaren i *tjänsten service-azureuser* Pod hittar inte en IP-adress för *serviceB. azureuser*.
1. Devspaces-proxy-behållaren i *tjänsten service. azureuser* Pod letar upp IP för *serviceB* i det överordnade utrymmet, som är *serviceB. default*.
1. Devspaces-proxy-behållaren i *tjänsten service. azureuser* Pod hittar IP för *serviceB. default* och dirigerar begäran till *serviceB. default* pod.
1. Devspaces-proxy-behållaren i *serviceB. default* -Pod tar emot begäran och dirigerar begäran till *serviceB* -programcontainern i *serviceB. default* pod.
1. *ServiceB* -programmet i *serviceB. default* -Pod returnerar ett svar till *tjänsten. azureuser* pod.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod tar emot svaret och dirigerar svaret till *tjänsten service-* program i *tjänst-azureuser-* pod.
1. *Tjänst* programmet tar emot svaret och returnerar sedan sitt eget svar.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod tar emot svaret från *tjänsten service-* program och dirigerar svaret till den ursprungliga anroparen utanför klustret.

All annan TCP-trafik som inte är HTTP passerar genom ingångs styrenheten och devspaces.

### <a name="how-running-your-code-is-configured"></a>Hur körning av din kod är konfigurerad

Azure dev Spaces använder `azds.yaml`-filen för att installera och konfigurera tjänsten. Kontrollanten använder egenskapen `install` i `azds.yaml`-filen för att installera Helm-diagrammet och skapa Kubernetes-objekten:

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

Som standard genererar kommandot `prep` Helm-diagrammet. Den anger också egenskapen *install. Chart* till katalogen i Helm-diagrammet. Om du vill använda ett Helm-diagram på en annan plats kan du uppdatera den här egenskapen för att använda den platsen.

När du installerar Helm-diagram ger Azure dev Spaces ett sätt att åsidosätta värden i Helm-diagrammet. Standardvärdena för Helm-diagrammet är i `charts/APP_NAME/values.yaml`.

Med hjälp av egenskapen *install. Values* kan du ange en eller flera filer som definierar värden som du vill ska ersättas i Helm-diagrammet. Om du till exempel vill ha ett värdnamn eller en databas konfiguration specifikt när du kör programmet i ett dev-utrymme kan du använda den här funktionen för åsidosättning. Du kan också lägga till en *?* i slutet av något av fil namnen för att ange det som valfritt.

Med egenskapen *install. Set* kan du konfigurera ett eller flera värden som du vill ersätta i Helm-diagrammet. Värden som kon figurer ATS i *install. Set* åsidosätter de värden som kon figurer ATS i filerna som anges i *install. Values*. Egenskaperna under *install. Set* är beroende av värdena i Helm-diagrammet och kan vara olika beroende på det genererade Helm-diagrammet.

I exemplet ovan anger egenskapen *install. Set. replicaCount* kontrollanten hur många instanser av programmet som ska köras i ditt dev-utrymme. Beroende på ditt scenario kan du öka det här värdet, men det påverkar hur du kopplar en fel sökare till programmets pod. Mer information finns i [fel söknings artikeln](troubleshooting.md).

I det genererade Helm-diagrammet är behållar avbildningen inställd på *{{. Values. image. databas}}: {{. Values. image. tag}}* . `azds.yaml`-filen definierar *install. Set. image. tag* -egenskapen som *$ (tag)* som standard som används som värde för *{{. Values. image. tag}}* . Genom att ställa in egenskapen *install. Set. image. tag* på det här sättet tillåter den att behållar avbildningen för programmet taggas på ett tydligt sätt när du kör Azure dev Spaces. I det här fallet märks avbildningen som *\<värde från avbildningen. databas >: $ (tag)* . Du måste använda variabeln *$ (tag)* som värdet för metoden *install. Set. image. tag* för att dev Spaces ska kunna identifiera och hitta behållaren i AKS-klustret.

I ovanstående exempel definierar `azds.yaml` *installationen. Set. ingress. hosts*. Egenskapen *install. Set. ingress. hosts* anger ett värdnamn för offentliga slut punkter. Den här egenskapen använder också *$ (spacePrefix)* , *$ (rootSpacePrefix)* och *$ (hostSuffix)* , som är värden som tillhandahålls av kontrollanten. 

*$ (SpacePrefix)* är namnet på den underordnade dev-ytan, som tar formen av *SPACENAME. s*. *$ (RootSpacePrefix)* är namnet på det överordnade utrymmet. Om *azureuser* till exempel är ett underordnat utrymme som *standard*är värdet för *$ (rootSpacePrefix)* *standard* och värdet för *$ (spacePrefix)* är *azureuser. s*. Om utrymmet inte är ett underordnat utrymme är *$ (spacePrefix)* tomt. Om *standard* utrymmet till exempel inte har något överordnat utrymme är värdet för *$ (rootSpacePrefix)* *standard* och värdet för *$ (spacePrefix)* är tomt. *$ (HostSuffix)* är ett DNS-suffix som pekar på ingångs styrenheten för Azure dev Spaces som körs i ditt AKS-kluster. Det här DNS-suffixet motsvarar en DNS-post med jokertecken, till exempel *\*. RANDOM_VALUE. EUs. azds. io*, som skapades när Azure dev Spaces-styrenheten lades till i ditt AKS-kluster.

I ovanstående `azds.yaml`-fil kan du också uppdatera *install. Set. ingress. hosts* för att ändra värd namnet för ditt program. Om du till exempel vill förenkla värd namnet för ditt program från *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* till *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix)* .

För att bygga behållaren för ditt program använder kontrollanten följande avsnitt i `azds.yaml` konfigurations filen:

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

Kontrollanten använder en Dockerfile för att skapa och köra ditt program.

Egenskapen *build. context* visar en lista över katalogen där Dockerfiles finns. Egenskapen *build. Dockerfile* definierar namnet på Dockerfile för att skapa produktions versionen av programmet. Egenskapen *Configurations. utvecklar. Build. Dockerfile* konfigurerar namnet på Dockerfile för utvecklings versionen av programmet.

Om du har olika Dockerfiles för utveckling och produktion kan du aktivera vissa saker under utveckling och inaktivera dessa objekt för produktions distributioner. Du kan till exempel aktivera fel sökning eller mer utförlig loggning under utveckling och inaktivera i en produktions miljö. Du kan också uppdatera dessa egenskaper om dina Dockerfiles har ett annat namn eller är på en annan plats.

För att hjälpa dig att snabbt iterera under utvecklingen kommer Azure dev Spaces att synkronisera ändringar från det lokala projektet och uppdatera ditt program stegvis. Avsnittet nedan i `azds.yaml` konfigurations filen används för att konfigurera synkronisering och uppdatering:

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

De filer och kataloger som ska synkronisera ändringar visas i filen *Configurations. utvecklar. container. Sync* . Dessa kataloger synkroniseras från början när du kör kommandot `up` och när ändringar identifieras. Om det finns ytterligare eller olika kataloger som du vill synkronisera till ditt dev-utrymme kan du ändra den här egenskapen.

Egenskapen *Configurations. utvecklar. container. ITER. buildCommands* anger hur programmet ska skapas i ett utvecklings scenario. Egenskapen *Configurations. utvecklar. container. Command* innehåller kommandot för att köra programmet i ett utvecklings scenario. Du kanske vill uppdatera någon av dessa egenskaper om det finns ytterligare bygg-eller körnings flaggor eller parametrar som du vill använda under utvecklingen.

*Konfigurationerna. utvecklar. container. ITER. processesToKill* listar de processer som ska avbrytas för att stoppa programmet. Du kanske vill uppdatera den här egenskapen om du vill ändra omstarts beteendet för ditt program under utveckling. Om du till exempel har uppdaterat *konfigurationerna. utvecklar. container. ITER. buildCommands* eller *Configurations. utvecklar. container. Command* för att ändra hur programmet byggs eller startas kan du behöva ändra vilka processer som har stoppats.

När du förbereder din kod med kommandot `azds prep` har du möjlighet att lägga till `--public`-flaggan. Om du lägger till flaggan `--public` skapas en offentligt tillgänglig URL för ditt program. Om du utelämnar den här flaggan är programmet endast tillgängligt i klustret eller med hjälp av localhost-tunneln. När du har kört kommandot `azds prep` kan du ändra den här inställningen för att ändra egenskapen *ingress. enabled* i `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Felsök koden

För Java-, .NET-och Node. js-program kan du felsöka ditt program som körs direkt i ditt utvecklings utrymme med Visual Studio Code eller Visual Studio. Visual Studio Code och Visual Studio ger verktyg för att ansluta till ditt utvecklings utrymme, starta ditt program och koppla en fel sökare. När du har kört `azds prep`kan du öppna projektet i Visual Studio Code eller Visual Studio. Visual Studio Code eller Visual Studio genererar egna konfigurationsfiler för anslutning som är skilda från att köra `azds prep`. I Visual Studio Code eller Visual Studio kan du ange Bryt punkter och starta ditt program i ditt utvecklings utrymme.

![Felsöka koden](media/get-started-node/debug-configuration-nodejs2.png)

När du startar ditt program med Visual Studio Code eller Visual Studio för fel sökning, hanterar de start och ansluter till ditt dev-utrymme på samma sätt som att köra `azds up`. Verktyget på klient sidan i Visual Studio Code och Visual Studio tillhandahåller också ytterligare en parameter med detaljerad information för fel sökning. Parametern innehåller namnet på fel söknings avbildningen, platsen för fel söknings programmet i fel söknings avbildningen och mål platsen i programmets behållare för att montera fel söknings mappen.

Fel söknings avbildningen bestäms automatiskt av klient sidans verktyg. Den använder en metod som liknar den som används under Dockerfile och Helm-diagrammet genereras när `azds prep`körs. När fel söknings programmet har monterats i programmets avbildning körs det med hjälp av `azds exec`.

## <a name="sharing-a-dev-space"></a>Dela ett dev-utrymme

När du arbetar med ett team kan du [dela ett dev-utrymme i ett helt Team](how-to/share-dev-spaces.md) och skapa härledda dev Spaces. Ett dev-utrymme kan användas av alla med deltagar åtkomst till dev-rummets resurs grupp.

Du kan också skapa ett nytt dev-utrymme som härleds från ett annat dev-utrymme. När du skapar ett härlett dev-utrymme läggs etiketten *azds.io/Parent-Space=Parent-Space-Name* till i namn området för den härledda dev-ytan. Dessutom delas alla program från det överordnade dev-utrymmet med det härledda dev-utrymmet. Om du distribuerar en uppdaterad version av ett program till det härledda dev-utrymmet finns det bara i det härledda dev-utrymmet och det överordnade dev-utrymmet förblir opåverkat. Du kan ha högst tre nivåer av härledda dev Spaces och *föräldrars* utrymmen.

Det härledda dev-utrymmet kommer också att dirigera begär Anden mellan sina egna program och de program som delas från dess överordnade. Routningen fungerar genom att försöka skicka begäran till ett program i det härledda dev-utrymmet och återgå till det delade programmet från det överordnade dev-utrymmet. Routningen kommer att återgå till det delade programmet på det föräldrarade utrymmet om programmet inte finns i det överordnade utrymmet.

Exempel:
* *Standard* för dev-utrymme har program *service* och *serviceB* .
* *Azureuser* för dev-ytan härleds från *standard*.
* En uppdaterad version av *servicen* har distribuerats till *azureuser*.

När du använder *azureuser*kommer alla begär Anden att *serva* att dirigeras till den uppdaterade versionen i *azureuser*. En begäran till *serviceB* kommer först att försöka dirigeras till *azureuser* -versionen av *serviceB*. Eftersom den inte finns kommer den att dirigeras till *standard* versionen av *serviceB*. Om *azureuser* -versionen av *servicen* tas bort, kommer alla begär Anden att *serva* att återgå till att använda *standard* versionen av *tjänsten*.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure dev Spaces finns i följande snabb starter:

* [Java med CLI och Visual Studio Code](quickstart-java.md)
* [.NET Core med CLI och Visual Studio Code](quickstart-netcore.md)
* [.NET Core med Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js med CLI och Visual Studio Code](quickstart-nodejs.md)

Information om hur du kommer igång med team utveckling finns i följande instruktions artiklar:

* [Grupp utveckling – Java med CLI och Visual Studio Code](team-development-java.md)
* [Grupp utveckling – .NET Core med CLI och Visual Studio Code](team-development-netcore.md)
* [Grupp utveckling – .NET Core med Visual Studio](team-development-netcore-visualstudio.md)
* [Grupp utveckling – Node. js med CLI och Visual Studio Code](team-development-nodejs.md)



[supported-regions]: about.md#supported-regions-and-configurations
