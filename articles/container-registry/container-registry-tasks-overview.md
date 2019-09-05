---
title: Automatisera skapande och korrigering av behållar avbildningar med Azure Container Registry uppgifter (ACR-aktiviteter)
description: En introduktion till ACR-aktiviteter, en uppsättning funktioner i Azure Container Registry som tillhandahåller säker, automatiserad version av behållar avbildning, hantering och korrigeringar i molnet.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 2d7237c1d142e9f7bb5a47294d1375040be43ac3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308026"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatisera behållar avbildnings versioner och underhåll med ACR-uppgifter

Behållare tillhandahåller nya nivåer av virtualisering, isolera program-och utvecklings beroenden från infrastruktur och drifts krav. Vad som är kvar är dock behovet av att adressera hur den här programvirtualiseringen hanteras och korrigeras över livs cykeln för behållare.

## <a name="what-is-acr-tasks"></a>Vad är ACR-uppgifter?

**ACR-aktiviteter** är en uppsättning funktioner i Azure Container Registry. Den tillhandahåller en molnbaserad behållar avbildnings avbildning för Linux, Windows och ARM och kan automatisera [operativ system och Ramverks korrigeringar](#automate-os-and-framework-patching) för Docker-behållare. ACR-aktiviteter utökar bara din "inre loop"-utvecklings cykel till molnet med behållar avbildnings avbildningar på begäran, men kan också aktivera automatiserade versioner av käll kods genomförande eller när en behållares bas avbildning uppdateras. Med bas avbildnings uppdatering utlösare kan du automatisera arbets flödet för operativ system-och program Ramverks uppdatering, upprätthålla säkra miljöer samtidigt som du följer huvud kontona för de oföränderliga behållarna.

Bygg och testa behållar avbildningar med ACR uppgifter på fyra sätt:

* [Snabb uppgift](#quick-task): Bygg och skicka container-avbildningar på begäran, i Azure, utan att behöva en lokal Docker-motor installation. Tänk `docker build` ,`docker push` i molnet. Bygg från lokal käll kod eller git-lagringsplats.
* [Bygg vid käll kods bekräftelse](#automatic-build-on-source-code-commit): Utlös en behållar avbildning som skapas automatiskt när kod allokeras till en git-lagringsplats.
* [Bygg uppdatering för bas avbildning](#automate-os-and-framework-patching): Utlös en behållar avbildning som skapas när avbildningens bas avbildning har uppdaterats.
* [Aktiviteter med flera steg](#multi-step-tasks): Definiera aktiviteter med flera steg som skapar avbildningar, kör behållare som kommandon och skicka avbildningar till ett register. Den här funktionen i ACR-aktiviteter har stöd för körning av aktiviteter på begäran och parallell avbildning, test och push-åtgärder.

## <a name="quick-task"></a>Snabb uppgift

Utvecklings cykeln för inre slingor, den iterativa processen med att skriva kod, skapa och testa ditt program innan du börjar använda käll kontroll, är verkligen början av livs cykel hantering av behållare.

Innan du genomför din första kodrad kan ACR-aktiviteternas [snabb uppgift](container-registry-tutorial-quick-task.md) tillhandahålla en integrerad utvecklings upplevelse genom att avlasta dina behållar avbildningar till Azure. Med snabb uppgifter kan du verifiera dina automatiserade versions definitioner och fånga eventuella problem innan du genomför din kod.

Med det välkända `docker build` formatet tar [AZ ACR build][az-acr-build] -kommandot i Azure CLI en [kontext](#context-locations) (uppsättningen filer som ska skapas), skickar ACR uppgifter och skickar som standard den inbyggda avbildningen till registret när den har slutförts.

En introduktion finns i snabb starten för att [skapa och köra en behållar avbildning](container-registry-quickstart-task-cli.md) i Azure Container Registry.  

ACR-uppgifter är utformade som en primitiv container-livs cykel. Du kan till exempel integrera ACR-uppgifter i din CI/CD-lösning. Genom att köra [AZ-inloggning][az-login] med ett [huvud namn för tjänsten][az-login-service-principal]kan din CI/CD-lösning utfärda [AZ ACR build][az-acr-build] -kommandon för att starta avbildnings byggen.

Lär dig hur du använder snabb uppgifter i den första självstudien för ACR-aktiviteter, [Bygg behållar avbildningar i molnet med Azure Container Registry uppgifter](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatisk generering vid käll kods incheckning

Använd ACR-aktiviteter för att automatiskt utlösa en behållar avbildning som skapas när kod allokeras till en git-lagringsplats i GitHub eller Azure DevOps. Bygg uppgifter, kan konfigureras med Azure CLI-kommandot [AZ ACR Task][az-acr-task], så att du kan ange en git-lagringsplats och eventuellt en gren och Dockerfile. När ditt team gör en kod till databasen, utlöser en ACR webhook en version av behållar avbildningen som definieras i lagrings platsen.

> [!IMPORTANT]
> Om du tidigare har skapat uppgifter i för hands versionen `az acr build-task` med kommandot måste du skapa dem på nytt med hjälp av kommandot [AZ ACR Task][az-acr-task] .

Lär dig hur du utlöser versioner av käll kods bekräftelse i självstudien för andra ACR uppgifter, [automatiserar behållar avbildnings avbildningar med Azure Container Registry uppgifter](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera korrigering av OS och ramverk

Kraften i ACR-aktiviteter för att verkligen förbättra arbets flödet för behållar bygget kommer från möjligheten att identifiera en uppdatering av en bas avbildning. När den uppdaterade bas avbildningen skickas till ditt register, eller om en bas avbildning uppdateras i en offentlig lagrings platsen, t. ex. i Docker Hub, kan ACR-aktiviteter automatiskt bygga program avbildningar baserat på den.

Behållar avbildningar kan delas i stor kategorisering i *bas* avbildningar och *program* avbildningar. Dina bas avbildningar omfattar vanligt vis de operativ system och program ramverk som ditt program bygger på, tillsammans med andra anpassningar. De här bas avbildningarna är vanligt vis baserade på offentliga överordnade avbildningar, till exempel: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]eller [Node. js][base-node]. Flera av dina program avbildningar kan dela en vanlig bas avbildning.

När en operativ system-eller app Framework-avbildning uppdateras av den överordnade behållaren, till exempel med en kritisk säkerhets korrigering för operativ system, måste du också uppdatera dina bas avbildningar för att inkludera den kritiska korrigeringen. Varje program avbildning måste sedan återskapas för att inkludera dessa uppströms korrigeringar som nu ingår i bas avbildningen.

Eftersom ACR-aktiviteter identifierar bas avbildnings beroenden dynamiskt när en behållar avbildning skapas, kan den identifiera när en program avbildnings bas avbildning uppdateras. Med en förkonfigurerad [build-uppgift](container-registry-tutorial-base-image-update.md#create-a-task) **återbyggs ACR-aktiviteter automatiskt varje program avbildning** åt dig. Med den här automatiska identifieringen och återuppbyggnaden sparar ACR-uppgifter den tid och ansträngning som normalt krävs för att manuellt spåra och uppdatera varje program avbildning som refererar till den uppdaterade bas avbildningen.

En ACR-uppgift spårar en bas avbildnings uppdatering när bas avbildningen finns på någon av följande platser:

* Samma Azure Container Registry där aktiviteten körs
* Ett annat Azure Container Registry i samma region 
* En offentlig lagrings platsen i Docker Hub
* En offentlig lagrings platsen i Microsoft Container Registry

Lär dig mer om uppdatering av operativ system och ramverk i självstudien för tredje ACR-aktiviteter, [Automatisera avbildningen med en bas avbildnings uppdatering med Azure Container Registry uppgifter](container-registry-tutorial-base-image-update.md).

## <a name="multi-step-tasks"></a>Uppgifter i flera steg

Aktiviteter med flera steg innehåller stegvisa aktivitets definitioner och körning för att skapa, testa och korrigera behållar avbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

Du kan till exempel skapa en aktivitet med flera steg som automatiserar följande:

1. Bygg en webb program avbildning
1. Kör behållaren för webb program
1. Bygg en test avbildning av webb program
1. Kör test behållaren för webb program som utför tester mot den program behållare som körs
1. Om testerna passerar skapar du ett Helm-diagram Arkiv paket
1. Utföra en `helm upgrade` användning med det nya Helm-diagrammets Arkiv paket

Med aktiviteter med flera steg kan du dela upp, köra och testa en bild i mer sammanställnings bara steg, med stöd för flera steg beroende. Med aktiviteter med flera steg i ACR-aktiviteter får du mer detaljerad kontroll över image bygge, testning och operativ system och ramverk för uppdatering av arbets flöden.

Lär dig mer om aktiviteter i flera steg i [köra åtgärder för att skapa, testa och korrigera flera steg i ACR uppgifter](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Kontext platser

I följande tabell visas några exempel på kontext platser som stöds för ACR-aktiviteter:

| Kontext plats | Beskrivning | Exempel |
| ---------------- | ----------- | ------- |
| Lokalt fil system | Filer i en katalog i det lokala fil systemet. | `/home/user/projects/myapp` |
| GitHub huvud gren | Filer inom den överordnade (eller andra standard) grenen av en GitHub-lagringsplats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-gren | En speciell gren av en GitHub-lagrings platsen.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-undermapp | Filer i en undermapp i en GitHub-lagrings platsen. Exempel på en kombination av en gren och undermappar specifikation. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Fjärran tarball | Filer i ett komprimerat Arkiv på en fjärran sluten Server. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Avbildnings plattformar

Som standard skapar ACR-uppgifter avbildningar för Linux OS och amd64-arkitekturen. `--platform` Ange taggen för att bygga Windows-avbildningar eller Linux-avbildningar för andra arkitekturer. Ange operativ systemet och eventuellt en arkitektur som stöds i OS/Architecture-format (till exempel `--platform Linux/arm`). För ARM-arkitekturer kan du välja att ange en variant i formatet OS/Architecture/variant (till `--platform Linux/arm64/v8`exempel):

| OS | Arkitektur|
| --- | ------- | 
| Linux | amd64<br/>koppling<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>Visa aktivitets loggar

Varje aktivitets körning genererar logg utdata som du kan kontrol lera för att avgöra om aktivitets stegen har körts. Om du använder kommandot [AZ ACR build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)eller [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) för att utlösa uppgiften, strömmas logg resultatet för aktivitets körningen till konsolen och lagras även för senare hämtning. När en aktivitet aktive ras automatiskt, till exempel genom en käll kods bekräftelse eller en bas avbildnings uppdatering, lagras endast aktivitets loggar. Visa loggarna för en uppgift som körs i Azure Portal eller Använd kommandot [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Från och med den 2019 juli kommer data och loggar för att köras i ett register att bevaras som standard i 30 dagar och sedan rensas automatiskt. Om du vill arkivera data för en aktivitets körning aktiverar du arkivering med hjälp av kommandot [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . I följande exempel aktive ras arkivering för aktiviteten kör *cf11* i Registry- *registret*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera operativ systemet och Ramverks korrigeringen genom att skapa dina behållar avbildningar i molnet kan du läsa [själv studie serien med ACR tasks](container-registry-tutorial-quick-task.md)i tre delar.

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
