---
title: Automatisera framework uppdatering med Azure Container Registry Build (ACR Build) av operativsystem och
description: En introduktion till ACR Build, en uppsättning funktioner i Azure Container Registry som ger säker, automatiserad behållare bild build och korrigeringar i molnet.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/28/2018
ms.author: marsma
ms.openlocfilehash: 532817c6289c1718fd82a502e04dc10715ee7203
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343108"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatisera framework uppdatering med ACR Build av operativsystem och

Behållare för nya nivåer av virtualisering, isolera program- och developer-beroenden från infrastrukturen och operativa krav. Vad är kvar, men är behovet av att hantera hur den här programvirtualisering är uppdaterad.

**ACR Build** är en uppsättning funktioner i Azure Container Registry. Det ger molnbaserade behållare bild byggnad för Linux, Windows och ARM och kan automatisera [framework uppdatering av operativsystem och](#automate-os-and-framework-patching) för Docker-behållare.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Vad är ACR Build?

Skapa för Azure Container Registry är en Azure-intern behållare bild build-tjänst. ACR Build aktiverar inre slingan utveckling i molnet med på begäran behållaravbildning bygger och automatiserade versioner på kod commit och base källbild uppdatera.

Utlösaren behållaravbildning bygger automatiskt när koden strävar efter att en Git-lagringsplats, eller när en behållare basavbildning har uppdaterats. Med basavbildningen update-utlösare, kan du automatisera ditt operativsystem och programramverk korrigeringar arbetsflöde, upprätthålla säkra miljöer under beaktande huvudkonton oföränderligt behållare.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Snabb Build: inre-loop utökats till molnet

I början av livscykelhantering startar innan utvecklare skickar sina första rader med kod. ACR Build [snabbt skapa](container-registry-tutorial-quick-build.md) funktionen gör det möjligt för en integrerad lokal inre slingan utvecklingsupplevelse, avlastning versioner till Azure. Med snabb skapas måste verifiera du dina automatiserade byggdefinitioner innan du checka in din kod.

Använda vanlig `docker build` format, den [az acr build] [ az-acr-build] kommando i Azure CLI tar en lokal kontext, skickar det till ACR Build-tjänsten och som standard skickar den skapade avbildningen till dess registret vid slutförande. ACR Build följer din geo-replikerade register aktiverar utspridda utvecklingsteam att utnyttja närmaste replikerade registret.

ACR Build är utformad som en primitiv behållare livscykel. Till exempel integrera ACR Build i din CI/CD-lösning. Genom att köra [az-inloggning] [ az-login] med en [tjänstens huvudnamn][az-login-service-principal], CI/CD-lösning kan sedan utfärdar [az acr skapa] [ az-acr-build] kommandon för att sätta igång bild versioner.

Lär dig hur du använder snabb bygger i den första kursen ACR Build [skapa behållaravbildningar i molnet med Azure Container Registry Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatisk byggd på källan kodgenomförande

Använd ACR Build att automatiskt utlösa en behållaravbildning build när koden strävar efter att en Git-lagringsplats. Build-uppgifter, kan konfigureras med Azure CLI-kommando [az acr build-aktivitet][az-acr-build-task], kan du ange en Git-lagringsplats och du kan också en gren och Dockerfile. När ditt team incheckningar kod till lagringsplatsen, utlöser en ACR Build-skapade webhook en version av den behållaravbildning som definierats i lagringsplatsen.

Lär dig hur du utlöser versioner på källan kodgenomförande i den andra självstudien ACR Build [automatisera behållaravbildning bygger med Azure Container Registry Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera framework uppdatering av operativsystem och

Kraften i ACR Build att verkligen förbättra din container build pipeline kommer från dess förmåga att identifiera en uppdatering av en basavbildning. När den uppdaterade basavbildningen skickas till registret, kan ACR Build automatiskt skapa alla programavbildningar som baseras på den.

Behållaravbildningar i stora drag kan kategoriseras i *grundläggande* avbildningar och *program* bilder. Din Källavbildningen inkluderar vanligtvis operativsystem och programramverk som ditt program har skapats, tillsammans med andra anpassningar. Dessa Källavbildningen är vanligtvis baserade på offentliga överordnade avbildningar, till exempel: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], eller [Node.js][base-node]. Flera av dina programavbildningar kan dela en gemensam basavbildning.

När en OS- eller app framework avbildning uppdateras av den överordnade Underhållaren, måste till exempel med en kritisk OS-säkerhetskorrigering du även uppdatera din grundläggande avbildningar för att inkludera kritiska korrigeringen. Varje programavbildningen måste sedan också byggas om så för att inkludera dessa överordnade korrigeringar ingår nu i din basavbildningen.

Eftersom ACR Build dynamiskt identifierar basavbildningen beroenden när den bygger en behållaravbildning, kan den identifiera när en programavbildning basavbildning har uppdaterats. Med en förkonfigurerad [Skapa uppgift](container-registry-tutorial-base-image-update.md#create-build-task), ACR Build sedan **återskapas automatiskt varje programavbildningen** åt dig. Med den här automatisk identifiering och återskapa, ACR Build sparar du tid och ansträngning som krävs för att manuellt spåra och uppdatera varje program bild refererar till din uppdaterade basavbildningen.

Lär dig mer om Operativsystemet och framework korrigeringar i den tredje självstudien ACR Build [automatisera bild bygger på grundläggande uppdateringar med Azure Container Registry Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> För den första förhandsversionen bygger basavbildningen uppdateringar utlösaren endast om båda bilderna grundläggande och program finns i samma Azure-behållarregister eller offentliga Docker Hub-databaser.

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera OS och framework uppdatering genom att skapa dina behållaravbildningar i molnet, Läs serien i tre delar ACR Build självstudiekursen.

> [!div class="nextstepaction"]
> [Skapa behållaravbildningar i molnet med Azure Container Registry Build](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
