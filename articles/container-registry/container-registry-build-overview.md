---
title: Automatisera OS och framework korrigering med Azure Container registret skapa (ACR Build)
description: En introduktion till ACR skapa en uppsättning funktioner i Azure Container registret som tillhandahåller säker, automatiserad behållaren image build och korrigering i molnet.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 3ef91270bceb5865bdbdf9c436e4519595a3dc09
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057782"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatisera OS och framework korrigering med ACR skapa

Behållare för nya nivåer av virtualisering, isolera program- och developer beroenden från infrastrukturen och operativa krav. Vad som finns kvar, men är behovet av att hantera hur den här programvirtualisering korrigeras.

**Skapa ACR**, en uppsättning funktioner i Azure Container registret ger inte bara interna behållaren image build funktion, men även automatiserar [OS och framework korrigering](#automate-os-and-framework-patching) för Docker-behållare.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Vad är ACR skapa?

Azure Container registret skapa är en Azure-intern behållaren image build-tjänst. ACR skapa aktiverar inre loop-utveckling i molnet med på begäran behållaren avbildning bygger och automatisk bygger på koden genomförande och base Källavbildningen uppdatera.

Utlösaren behållaren image skapar automatiskt när koden strävar efter att en Git-lagringsplats, eller när en behållare basavbildning uppdateras. Med basavbildning update-utlösare, kan du automatisera ditt operativsystem och programramverk korrigering arbetsflöde, behåller säkra miljöer när efterlever säkerhetsobjekt ändras behållare.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Snabbt bygga: inre-loop utökats till molnet

I början av livscykelhantering startar innan utvecklare bekräfta sin första rader med kod. ACR skapa [snabbt skapa](container-registry-tutorial-quick-build.md) funktionen gör det möjligt för en integrerad lokala inre loop utvecklingsarbetet, genom att avlasta versioner till Azure. Med snabb versioner, kan du kontrollera automatisk build definitionerna innan du genomför din kod.

Med hjälp av vanliga `docker build` format, den [az acr build] [ az-acr-build] kommando i Azure CLI tar en lokal kontext, skickar den till ACR byggtjänsten och som standard skickar inbyggd avbildningen till dess registret på slutförande. ACR skapa följer din georeplikerad register aktiverar spridda utvecklingsgrupper utnyttja närmaste replikerade registret. Under förhandsgranskningen gör är ACR build tillgängligt i östra USA och Västeuropa regioner.

Skapa ACR är utformad som en primitiv behållaren livscykel. Till exempel integrera ACR skapa CI/CD-lösning. Genom att köra [az inloggningen] [ az-login] med en [tjänstens huvudnamn][az-login-service-principal], CI/CD-lösning kan sedan utfärdar [az acr skapa] [ az-acr-build] kommandon som startar image build-versioner.

Lär dig hur du använder snabb bygger i den första kursen ACR skapa [skapa behållaren bilder i molnet med Azure Container registret skapa](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatisk version på källan kod genomförande

Använd ACR skapa ska utlösa en avbildning i behållaren automatiskt bygga när koden strävar efter att en Git-lagringsplats. Skapa uppgifter som kan konfigureras med Azure CLI-kommandot [az acr build-aktivitet][az-acr-build-task], kan du ange en Git-lagringsplats och eventuellt en gren och Dockerfile. När din grupp sparar koden i databasen, utlöser en webhook ACR skapa skapas en version av behållare avbildningen som definierats i lagringsplatsen.

Lär dig hur du utlöser versioner på källan kod commit för andra ACR skapa kursen [automatisera behållaren image bygger med Azure Container registret skapa](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera OS och framework korrigering

Kraften i ACR skapa att förbättra din behållaren build pipeline verkligen kommer från förmåga att identifiera en uppdatering för en grundläggande bild. När uppdaterade basavbildningen skickas till registret kan ACR skapa automatiskt skapa alla programavbildningar som baseras på den.

Behållaren bilder brett kan grupperas i *grundläggande* bilder och *programmet* bilder. Bilderna grundläggande inkluderar vanligtvis operativsystem och programramverk som ditt program skapas tillsammans med andra anpassningar. Grundläggande avbildningarna är vanligtvis baserade på offentliga överordnade bilder, till exempel [Alpine Linux] [ base-alpine] eller [Node.js][base-node]. Flera av dina programavbildningar kanske delar en gemensam grundläggande bild.

När en OS- eller app framework avbildning uppdateras av den överordnade Underhållaren, måste till exempel med en kritisk OS-säkerhetskorrigering du även uppdatera dina grundläggande avbildningar för kritiska korrigeringsfil. Varje programavbildning måste sedan också återskapas för att inkludera dessa överordnade korrigeringar ingår nu i din grundläggande bild.

Eftersom ACR skapa dynamiskt identifierar basavbildning beroenden när den bygger en avbildning av behållare, kan det upptäcka när en programavbildning basavbildning uppdateras. Med en förkonfigurerad [Skapa uppgift](container-registry-tutorial-base-image-update.md#create-build-task), ACR skapa sedan **återskapas automatiskt varje programavbildning** åt dig. Med den här automatisk identifiering och återskapa, skapa ACR sparar du tid och arbete som krävs för att manuellt spåra och uppdatera varje program bild refererar till den uppdaterade basavbildningen.

Lär dig mer om OS och framework korrigering i tredje ACR skapa självstudiekursen [automatisera avbildningen bygger på basavbildning uppdatering med Azure Container registret skapa](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> För förhandsversionen av inledande bygger basavbildning uppdateringar utlösaren endast när båda bilderna bas- och program finns i samma Azure-behållaren registret.

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera OS och ramverk för uppdatering genom att skapa behållaren bilder i molnet, se tre delar ACR skapa självstudiekursen serien.

> [!div class="nextstepaction"]
> [Skapa behållaren bilder i molnet med Azure Container registret skapa](container-registry-tutorial-quick-build.md)

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
