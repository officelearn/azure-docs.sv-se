---
title: Översikt över ACR-uppgifter
description: En introduktion till ACR-uppgifter, en uppsättning funktioner i Azure Container Registry som tillhandahåller säker, automatiserad containeravbildningsversion, hantering och korrigering i molnet.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087286"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatisera behållaravbildningsversioner och underhåll med ACR-uppgifter

Behållare ger nya nivåer av virtualisering, isolera program- och utvecklarberoenden från infrastruktur- och driftskrav. Det som återstår är dock behovet av att ta itu med hur den här programvirtualiseringen hanteras och korrigeras över behållarens livscykel.

## <a name="what-is-acr-tasks"></a>Vad är ACR-uppgifter?

**ACR-uppgifter** är en uppsättning funktioner i Azure Container Registry. Det ger molnbaserad containeravbildningsuppbyggnad för [plattformar](#image-platforms) som Linux, Windows och ARM, och kan automatisera [OS och ramkorrigering](#automate-os-and-framework-patching) för dina Docker-behållare. ACR-uppgifter utökar inte bara din "inre loop" utvecklingscykel till molnet med behållarens avbildningsversioner på begäran, utan möjliggör också automatiserade versioner som utlöses av källkodsuppdateringar, uppdateringar av en behållares basavbildning eller timers. Med basavbildningsuppdateringsutlösare kan du till exempel automatisera arbetsflödet för os- och programramskorrigering, underhålla säkra miljöer samtidigt som du följer principerna för oföränderliga behållare.

## <a name="task-scenarios"></a>Aktivitetsscenarier

ACR-uppgifter stöder flera scenarier för att skapa och underhålla behållaravbildningar och andra artefakter. Mer information finns i följande avsnitt i den här artikeln.

* **[Snabb uppgift](#quick-task)** - Skapa och skicka en enda behållaravbildning till ett behållarregister på begäran, i Azure, utan att behöva en lokal Docker Engine-installation. Tänk `docker build` `docker push` , i molnet.
* **Automatiskt utlösta uppgifter** - Aktivera en eller flera *utlösare* för att skapa en avbildning:
  * **[Utlösare vid uppdatering av källkod](#trigger-task-on-source-code-update)** 
  * **[Utlösare vid basavbildningsuppdatering](#automate-os-and-framework-patching)** 
  * **[Utlösare enligt ett schema](#schedule-a-task)** 
* **[Flerstegsuppgift](#multi-step-tasks)** - Utöka acr-uppgifternas enhets- och push-kapacitet med flera steg, flera behållarbaserade arbetsflöden. 

Varje ACR-aktivitet har en associerad [källkodskontext](#context-locations) - platsen för en uppsättning källfiler som används för att skapa en behållaravbildning eller annan artefakt. Exempel på kontexter är en Git-databas eller ett lokalt filsystem.

Aktiviteter kan också dra nytta av [körningsvariabler](container-registry-tasks-reference-yaml.md#run-variables), så att du kan återanvända aktivitetsdefinitioner och standardisera taggar för bilder och artefakter.

## <a name="quick-task"></a>Snabb uppgift

Den inre kretsloppet utvecklingscykel, den iterativ processen att skriva kod, bygga och testa ditt program innan de bestämmer sig för källkontroll, är verkligen början på behållarens livscykelhantering.

Innan du genomför din första kodrad kan ACR-uppgifters [snabbuppgiftsfunktion](container-registry-tutorial-quick-task.md) ge en integrerad utvecklingsupplevelse genom att avlasta behållaravbildningsversionerna till Azure. Med snabba uppgifter kan du verifiera dina automatiska byggdefinitioner och fånga upp potentiella problem innan du bestämmer koden.

Med hjälp `docker build` av det välbekanta formatet tar kommandot [az acr build][az-acr-build] i Azure CLI en [kontext](#context-locations) (uppsättningen filer att skapa), skickar den ACR-uppgifter och skickar som standard den inbyggda avbildningen till registret när den är klar.

En introduktion finns i snabbstarten för att [skapa och köra en behållaravbildning](container-registry-quickstart-task-cli.md) i Azure Container Registry.  

ACR-uppgifter är utformade som en primitiv behållarelivscykel. Integrera till exempel ACR-uppgifter i ci/cd-lösningen. Genom att köra [az inloggning][az-login] med en [tjänst huvudnamn,][az-login-service-principal]din CI / CD-lösning kan sedan utfärda [az acr bygga][az-acr-build] kommandon för att sparka igång bild bygger.

Lär dig hur du använder snabbuppgifter i den första acr-uppgiftsstudien, [Skapa behållaravbildningar i molnet med Azure Container Registry Tasks](container-registry-tutorial-quick-task.md).

> [!TIP]
> Om du vill skapa och skicka en avbildning direkt från källkoden, utan en Dockerfile, tillhandahåller Azure Container Registry kommandot [az acr pack build][az-acr-pack-build] (förhandsversion). Det här verktyget skapar och skickar en avbildning från programmets källkod med [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Utlösaruppgift för uppdatering av källkod

Utlösa en behållaravbildningsversion eller flerstegsuppgift när koden har bekräftats, eller en pull-begäran görs eller uppdateras, till en offentlig eller privat Git-databas i GitHub eller Azure DevOps. Konfigurera till exempel en bygguppgift med Azure CLI-kommandot [az acr-uppgift skapa][az-acr-task-create] genom att ange en Git-databas och eventuellt en gren och Dockerfile. När gruppen uppdaterar koden i databasen utlöser en ACR-aktivitetsskapad webhook en version av behållaravbildningen som definierats i reporäntan. 

ACR-uppgifter stöder följande utlösare när du anger en Git-repo som aktivitetens kontext:

| Utlösare | Aktiverat som standard |
| ------- | ------------------ |
| Checka in | Ja |
| Pull-begäran | Inga |

Om du vill konfigurera en utlösare för källkodsuppdatering måste du ange uppgiften en personlig åtkomsttoken (PAT) för att ange webhooken i den offentliga eller privata GitHub- eller Azure DevOps-repoen.

> [!NOTE]
> För närvarande stöder ACR-uppgifter inte utlösare för commit eller pull-begäran i GitHub Enterprise-repos.

Lär dig hur du utlöser versioner av källkodsförsetagande i den andra acr-uppgiftsstudien, [Automatisera behållaravbildningsversioner med Azure Container Registry Tasks](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera os- och ramkorrigering

Kraften i ACR-uppgifter för att verkligen förbättra din behållare bygga arbetsflöde kommer från dess förmåga att upptäcka en uppdatering till en *bas avbildning*. En funktion i de flesta behållaravbildningar, en basavbildning är en överordnad bild som en eller flera programavbildningar baseras på. Basavbildningar innehåller vanligtvis operativsystemet och ibland programramverk. 

Du kan ställa in en ACR-aktivitet för att spåra ett samband på en basavbildning när den skapar en programavbildning. När den uppdaterade basavbildningen flyttas till registret, eller om en basavbildning uppdateras i en offentlig repa, till exempel i Docker Hub, kan ACR-uppgifter automatiskt skapa programavbildningar baserat på den.
Med den här automatiska identifieringen och ombyggnaden sparar ACR-uppgifter den tid och ansträngning som normalt krävs för att manuellt spåra och uppdatera varje programavbildning som refererar till den uppdaterade basavbildningen.

Läs mer om utlösare för [basavbildningsuppdatering](container-registry-tasks-base-images.md) för ACR-uppgifter. Och lär dig hur du utlöser en avbildningsversion när en basavbildning skjuts till ett behållarregister i självstudien [Automatisera behållaravbildningsversioner när en basavbildning uppdateras i ett Azure-behållarregister](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Schemalägga en aktivitet

Du kan också schemalägga en aktivitet genom att ställa in en eller flera *timerutlösare* när du skapar eller uppdaterar aktiviteten. Schemaläggning av en aktivitet är användbart för att köra behållararbetsbelastningar enligt ett definierat schema, eller köra underhållsåtgärder eller tester på avbildningar som regelbundet skjuts till registret. Mer information finns i [Köra en ACR-aktivitet enligt ett definierat schema](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Uppgifter i flera steg

Multistegsuppgifter ger stegbaserad uppgiftsdefinition och körning för att skapa, testa och korrigera behållaravbildningar i molnet. Aktivitetssteg som definierats i en [YAML-fil](container-registry-tasks-reference-yaml.md) anger enskilda bygg- och push-åtgärder för behållaravbildningar eller andra artefakter. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

Du kan till exempel skapa en aktivitet i flera steg som automatiserar följande:

1. Skapa en avbildning av webbprogram
1. Kör behållaren för webbprogram
1. Skapa en testavbildning för webbprogram
1. Kör testbehållaren för webbprogram, som utför tester mot programbehållaren som körs
1. Om testerna går igenom, bygga ett Helm-diagramarkivpaket
1. Utföra `helm upgrade` ett med det nya Helm-diagramarkivpaketet

Med flera steg kan du dela upp byggnaden, löpningen och testningen av en avbildning i mer komposterbara steg med beroendestöd mellan steg. Med flerstegsuppgifter i ACR-uppgifter har du mer detaljerad kontroll över arbetsflöden för bilduppbyggnad, testning och operativsystem och ramkorrigering.

Lär dig mer om flerstegsaktiviteter i [Kör flerstegs-, test- och korrigeringsuppgifter i ACR-uppgifter](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Sammanhangsplatser

I följande tabell visas några exempel på kontextplatser som stöds för ACR-uppgifter:

| Kontextplats | Beskrivning | Exempel |
| ---------------- | ----------- | ------- |
| Lokalt filsystem | Filer i en katalog i det lokala filsystemet. | `/home/user/projects/myapp` |
| GitHub-huvudgren | Filer i huvudgrenen (eller någon annan standard)-gren i en offentlig eller privat GitHub-databas.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-gren | Specifik gren av en offentlig eller privat GitHub-repo.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Undermappen GitHub | Filer i en undermapp i en offentlig eller privat GitHub-repo. Exempel visar en kombination av en specifikation för gren och undermappar. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub-commit | Specifikt åtagande i en offentlig eller privat GitHub-repo. Exempel visar en kombination av en commit-hash -specifikation (SHA) och undermapp. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Undermapp för Azure DevOps | Filer i en undermapp i en offentlig eller privat Azure-repo. Exempel visar en kombination av specifikation av gren och undermapp. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Avlägsna tarball | Filer i ett komprimerat arkiv på en fjärrwebbserver. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> När du använder en privat Git-repo som en kontext för en uppgift måste du ange en personlig åtkomsttoken (PAT).

## <a name="image-platforms"></a>Bildplattformar

Som standard bygger ACR-uppgifter bilder för Linux OS och amd64-arkitekturen. Ange `--platform` taggen för att skapa Windows-avbildningar eller Linux-avbildningar för andra arkitekturer. Ange operativsystemet och eventuellt en arkitektur som stöds i `--platform Linux/arm`OS/arkitekturformat (till exempel ). För ARM-arkitekturer anger du eventuellt en variant i OS/arkitektur/variantformat (till `--platform Linux/arm64/v8`exempel):

| Operativsystem | Arkitektur|
| --- | ------- | 
| Linux | amd64 (på ett sätt som är upp till<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 (på ett sätt som är upp till |

## <a name="view-task-output"></a>Visa aktivitetens utdata

Varje aktivitetskörning genererar loggutdata som du kan granska för att avgöra om aktivitetsstegen kördes. När du utlöser en aktivitet manuellt strömmas loggutdata för aktivitetskörningen till konsolen och lagras även för senare hämtning. När en aktivitet utlöses automatiskt, till exempel genom en källkodsavstämpling eller en basavbildningsuppdatering, lagras aktivitetsloggar bara. Visa körningsloggarna i Azure-portalen eller använd kommandot [az acr task logs.](/cli/azure/acr/task#az-acr-task-logs)

Läs mer om [hur du visar och hanterar uppgiftsloggar](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera behållaravbildningsversioner och underhåll i molnet kan du kolla in [självstudieserien ACR-uppgifter](container-registry-tutorial-quick-task.md).

Installera eventuellt [Docker-tillägget för Visual Studio-kod](https://code.visualstudio.com/docs/azure/docker) och [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure-behållarregister. Hämta och skicka avbildningar till ett Azure-behållarregister eller kör ACR-uppgifter, allt i Visual Studio Code.

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
