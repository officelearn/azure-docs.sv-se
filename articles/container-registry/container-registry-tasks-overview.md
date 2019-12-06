---
title: Översikt över ACR-uppgifter
description: En introduktion till ACR-aktiviteter, en uppsättning funktioner i Azure Container Registry som tillhandahåller säker, automatiserad version av behållar avbildning, hantering och korrigeringar i molnet.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 96997f963f0bcb319d5318e2dd88a6e1e21fb36b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840773"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatisera behållar avbildnings versioner och underhåll med ACR-uppgifter

Behållare tillhandahåller nya nivåer av virtualisering, isolera program-och utvecklings beroenden från infrastruktur och drifts krav. Vad som är kvar är dock behovet av att adressera hur den här programvirtualiseringen hanteras och korrigeras över livs cykeln för behållare.

## <a name="what-is-acr-tasks"></a>Vad är ACR-uppgifter?

**ACR-aktiviteter** är en uppsättning funktioner i Azure Container Registry. Den innehåller molnbaserad behållar avbildnings avbildning för [plattformar](#image-platforms) , inklusive Linux, Windows och arm, och kan automatisera [uppdatering av operativ system och ramverk](#automate-os-and-framework-patching) för dina Docker-behållare. ACR-aktiviteter utökar inte bara din "inre loop"-utvecklings cykel till molnet med behållar avbildnings avbildningar på begäran, men aktiverar även automatiserade versioner som utlöses av käll kods uppdateringar, uppdateringar till en behållares bas avbildning eller timers. Med bas avbildnings uppdatering utlösare kan du till exempel automatisera arbets flödet för operativ system-och program ramverk, upprätthålla säkra miljöer samtidigt som principerna för ej skyddade behållare används.

## <a name="task-scenarios"></a>Uppgifts scenarier

ACR-aktiviteter stöder flera scenarier för att bygga och underhålla behållar avbildningar och andra artefakter. Mer information finns i följande avsnitt i den här artikeln.

* **[Snabb uppgift](#quick-task)** – bygga och skicka en enda behållar avbildning till ett behållar register på begäran, i Azure, utan att behöva en lokal Docker-motor installation. Tänk `docker build``docker push` i molnet.
* **Automatiskt utlöst uppgift** – aktivera en eller flera *utlösare* för att skapa en avbildning:
  * **[Utlös vid uppdatering av käll kod](#trigger-task-on-source-code-update)** 
  * **[Utlös vid uppdatering av bas avbildning](#automate-os-and-framework-patching)** 
  * **[Utlös enligt ett schema](#schedule-a-task)** 
* **[Aktivitet med flera steg](#multi-step-tasks)** – utöka funktionerna för att bygga och skicka ACR med flera steg, flera behållare baserade arbets flöden. 

Varje ACR-aktivitet har en associerad [käll kods kontext](#context-locations) – platsen för en uppsättning källfiler som används för att skapa en behållar avbildning eller en annan artefakt. Exempel på kontexter innehåller en git-lagringsplats eller ett lokalt fil system.

Aktiviteter kan också dra nytta av att [köra variabler](container-registry-tasks-reference-yaml.md#run-variables), så att du kan återanvända aktivitets definitioner och standardisera taggar för bilder och artefakter.

## <a name="quick-task"></a>Snabb uppgift

Utvecklings cykeln för inre slingor, den iterativa processen med att skriva kod, skapa och testa ditt program innan du börjar använda käll kontroll, är verkligen början av livs cykel hantering av behållare.

Innan du genomför din första kodrad kan ACR-aktiviteternas [snabb uppgift](container-registry-tutorial-quick-task.md) tillhandahålla en integrerad utvecklings upplevelse genom att avlasta dina behållar avbildningar till Azure. Med snabb uppgifter kan du verifiera dina automatiserade versions definitioner och fånga eventuella problem innan du genomför din kod.

Med hjälp av det bekanta `docker build`-formatet tar [AZ ACR build][az-acr-build] -kommandot i Azure CLI en [kontext](#context-locations) (uppsättningen filer som ska skapas), skickar ACR uppgifter och skickar som standard den inbyggda avbildningen till registret när den har slutförts.

En introduktion finns i snabb starten för att [skapa och köra en behållar avbildning](container-registry-quickstart-task-cli.md) i Azure Container Registry.  

ACR-uppgifter är utformade som en primitiv container-livs cykel. Du kan till exempel integrera ACR-uppgifter i din CI/CD-lösning. Genom att köra [AZ-inloggning][az-login] med ett [huvud namn för tjänsten][az-login-service-principal]kan din CI/CD-lösning utfärda [AZ ACR build][az-acr-build] -kommandon för att starta avbildnings byggen.

Lär dig hur du använder snabb uppgifter i den första självstudien för ACR-aktiviteter, [Bygg behållar avbildningar i molnet med Azure Container Registry uppgifter](container-registry-tutorial-quick-task.md).

> [!TIP]
> Om du vill bygga och skicka en avbildning direkt från käll koden utan en Dockerfile tillhandahåller Azure Container Registry [AZ ACR Pack build][az-acr-pack-build] -kommandot (för hands version). Verktyget skapar och skickar en avbildning från program käll koden med hjälp av [inbyggda Cloud-Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Utlös aktivitet för uppdatering av käll kod

Utlös en behållar avbildnings version eller flera stegs aktivitet när koden har genomförts eller en pull-begäran görs eller uppdateras till en offentlig eller privat git-lagringsplats i GitHub eller Azure DevOps. Du kan till exempel konfigurera en build-aktivitet med Azure CLI-kommandot [AZ ACR Task Create][az-acr-task-create] genom att ange en git-lagringsplats och eventuellt en gren och Dockerfile. När ditt team uppdaterar kod i databasen, utlöser en ACR webhook en version av behållar avbildningen som definieras i lagrings platsen. 

ACR-aktiviteter stöder följande utlösare när du anger en git-lagrings platsen som aktivitetens kontext:

| Utlösare | Aktiverat som standard |
| ------- | ------------------ |
| Checka in | Ja |
| Pull-begäran | Nej |

Om du vill konfigurera en uppdaterings utlösare för käll koden måste du ange uppgiften en personlig åtkomsttoken (PAT) för att ställa in webhooken i den offentliga eller privata GitHub eller Azure DevOps lagrings platsen.

> [!NOTE]
> För närvarande stöder ACR-aktiviteter inte utlösare för commit eller pull-begäranden i GitHub Enterprise databaser.

Lär dig hur du utlöser versioner av käll kods bekräftelse i självstudien för andra ACR uppgifter, [automatiserar behållar avbildnings avbildningar med Azure Container Registry uppgifter](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera korrigering av OS och ramverk

Kraften i ACR-aktiviteter för att verkligen förbättra arbets flödet för behållar bygget kommer från möjligheten att identifiera en uppdatering av en bas avbildning. När den uppdaterade bas avbildningen skickas till ditt register, eller om en bas avbildning uppdateras i en offentlig lagrings platsen, t. ex. i Docker Hub, kan ACR-aktiviteter automatiskt bygga program avbildningar baserat på den.

Behållar avbildningar kan delas i stor kategorisering i *bas* avbildningar och *program* avbildningar. Dina bas avbildningar omfattar vanligt vis de operativ system och program ramverk som ditt program bygger på, tillsammans med andra anpassningar. De här bas avbildningarna är vanligt vis baserade på offentliga överordnade avbildningar, till exempel: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]eller [Node. js][base-node]. Flera av dina program avbildningar kan dela en vanlig bas avbildning.

När en operativ system-eller app Framework-avbildning uppdateras av den överordnade behållaren, till exempel med en kritisk säkerhets korrigering för operativ system, måste du också uppdatera dina bas avbildningar för att inkludera den kritiska korrigeringen. Varje program avbildning måste sedan återskapas för att inkludera dessa uppströms korrigeringar som nu ingår i bas avbildningen.

Eftersom ACR-aktiviteter identifierar bas avbildnings beroenden dynamiskt när en behållar avbildning skapas, kan den identifiera när en program avbildnings bas avbildning uppdateras. Med en förkonfigurerad [build-uppgift](container-registry-tutorial-base-image-update.md#create-a-task) **återbyggs ACR-aktiviteter automatiskt varje program avbildning** åt dig. Med den här automatiska identifieringen och återuppbyggnaden sparar ACR-uppgifter den tid och ansträngning som normalt krävs för att manuellt spåra och uppdatera varje program avbildning som refererar till den uppdaterade bas avbildningen.

För avbildningar som bygger på en Dockerfile, spårar en ACR-uppgift en bas avbildnings uppdatering när bas avbildningen finns på någon av följande platser:

* Samma Azure Container Registry där aktiviteten körs
* Ett annat Azure Container Registry i samma region 
* En offentlig lagrings platsen i Docker Hub
* En offentlig lagrings platsen i Microsoft Container Registry

> [!NOTE]
> * Uppdaterings utlösaren för bas avbildning är aktive rad som standard i en ACR-aktivitet. 
> * För närvarande spårar ACR-uppgifter bara bas avbildnings uppdateringar för program (*runtime*)-avbildningar. ACR-aktiviteter spårar inte bas avbildnings uppdateringar för mellanliggande (*buildtime*) avbildningar som används i multi-Stage-Dockerfiles. 

Lär dig mer om uppdatering av operativ system och ramverk i självstudien för tredje ACR-aktiviteter, [Automatisera avbildningen med en bas avbildnings uppdatering med Azure Container Registry uppgifter](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Schemalägga en aktivitet

Du kan också schemalägga en aktivitet genom att ställa in en eller flera *timer-utlösare* när du skapar eller uppdaterar aktiviteten. Schemaläggning av en aktivitet är användbart för att köra arbets belastningar för behållare enligt ett definierat schema, eller köra underhålls åtgärder eller tester på avbildningar som skickas regelbundet till registret. Mer information finns i [köra en ACR-aktivitet enligt ett definierat schema](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Uppgifter i flera steg

Aktiviteter med flera steg innehåller stegvisa aktivitets definitioner och körning för att skapa, testa och korrigera behållar avbildningar i molnet. Uppgifts steg som definieras i en [yaml-fil](container-registry-tasks-reference-yaml.md) anger enskilda bygg-och push-åtgärder för behållar avbildningar eller andra artefakter. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

Du kan till exempel skapa en aktivitet med flera steg som automatiserar följande:

1. Bygg en webb program avbildning
1. Kör behållaren för webb program
1. Bygg en test avbildning av webb program
1. Kör test behållaren för webb program, som utför tester mot den program behållare som körs
1. Om testerna passerar skapar du ett Helm-diagram Arkiv paket
1. Utföra en `helm upgrade` med det nya Helm-diagrammets Arkiv paket

Med aktiviteter med flera steg kan du dela upp, köra och testa en bild i mer sammanställnings bara steg, med stöd för flera steg beroende. Med aktiviteter med flera steg i ACR-aktiviteter får du mer detaljerad kontroll över image bygge, testning och operativ system och ramverk för uppdatering av arbets flöden.

Lär dig mer om aktiviteter i flera steg i [köra åtgärder för att skapa, testa och korrigera flera steg i ACR uppgifter](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Kontext platser

I följande tabell visas några exempel på kontext platser som stöds för ACR-aktiviteter:

| Kontext plats | Beskrivning | Exempel |
| ---------------- | ----------- | ------- |
| Lokalt fil system | Filer i en katalog i det lokala fil systemet. | `/home/user/projects/myapp` |
| GitHub huvud gren | Filer i huvud gruppen (eller andra standard) i en offentlig eller privat GitHub-lagringsplats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-gren | En speciell gren av en offentlig eller privat GitHub-lagrings platsen.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-undermapp | Filer i en undermapp i en offentlig eller privat GitHub-lagrings platsen. Exempel på en kombination av en gren och undermappar specifikation. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure dataDevOpss-undermapp | Filer i en undermapp i en offentlig eller privat Azure-lagrings platsen. Exempel på en kombination av förgrening och undermappar specifikation. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Fjärran tarball | Filer i ett komprimerat Arkiv på en fjärran sluten Server. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> När du använder en privat git-lagrings platsen som en kontext för en aktivitet måste du ange en personlig åtkomsttoken (PAT).

## <a name="image-platforms"></a>Avbildnings plattformar

Som standard skapar ACR-uppgifter avbildningar för Linux OS och amd64-arkitekturen. Ange `--platform`-taggen för att bygga Windows-avbildningar eller Linux-avbildningar för andra arkitekturer. Ange operativ systemet och eventuellt en arkitektur som stöds i OS/Architecture-format (till exempel `--platform Linux/arm`). För ARM-arkitekturer kan du välja att ange en variant i formatet OS/Architecture/variant (till exempel `--platform Linux/arm64/v8`):

| OS | Arkitektur|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>Visa aktivitets loggar

Varje aktivitets körning genererar logg utdata som du kan kontrol lera för att avgöra om aktivitets stegen har körts. Om du använder kommandot [AZ ACR build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)eller [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) för att utlösa uppgiften, strömmas logg resultatet för aktivitets körningen till konsolen och lagras även för senare hämtning. När en aktivitet aktive ras automatiskt, till exempel genom en käll kods bekräftelse eller en bas avbildnings uppdatering, lagras endast aktivitets loggar. Visa loggarna för en uppgift som körs i Azure Portal eller Använd kommandot [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Som standard behålls data och loggar för aktivitet i ett register i 30 dagar och rensas sedan automatiskt. Om du vill arkivera data för en aktivitets körning aktiverar du arkivering med hjälp av kommandot [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . I följande exempel aktive ras arkivering för aktiviteten kör *cf11* i Registry- *registret*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera behållar avbildnings versioner och underhåll i molnet kan du ta en titt på [själv studie serien med ACR tasks](container-registry-tutorial-quick-task.md).

Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure Container register. Hämta och push-avbildningar till ett Azure Container Registry, eller kör ACR-aktiviteter, allt i Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
