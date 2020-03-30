---
title: Grundläggande bilduppdateringar - Uppgifter
description: Lär dig mer om basavbildningar för programbehållarevbildningar och om hur en basavbildningsuppdatering kan utlösa en Azure Container Registry-uppgift.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617936"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Om basavbildningsuppdateringar för ACR-uppgifter

Den här artikeln innehåller bakgrundsinformation om uppdateringar av ett programs basavbildning och hur dessa uppdateringar kan utlösa en Azure Container Registry-uppgift.

## <a name="what-are-base-images"></a>Vad är basbilder?

Dockerfiler som definierar de flesta behållaravbildningar anger en överordnad bild som bilden baseras på, ofta kallad *basavbildningen*. Basavbildningar innehåller vanligtvis operativsystemet, till exempel [Alpine Linux][base-alpine] eller [Windows Nano Server][base-windows], där resten av containerns lager tillämpas. De kan även innehålla programramverk som [Node.js][base-node] eller [.NET Core][base-dotnet]. Dessa basbilder är själva vanligtvis baserade på offentliga uppströmsbilder. Flera av dina programbilder kan dela en gemensam basavbildning.

En basavbildning uppdateras ofta av avbildningens underhållare med nya funktioner och förbättringar av operativsystem eller ramverk i avbildningen. Säkerhetskorrigeringar är en annan vanlig orsak till att en basavbildning uppdateras. När dessa uppströmsuppdateringar inträffar måste du också uppdatera basavbildningarna så att de innehåller den kritiska korrigeringen. Varje programavbildning måste sedan också byggas om för att inkludera dessa uppströmskorrigeringar som nu ingår i basavbildningen.

I vissa fall, till exempel ett privat utvecklingsteam, kan en basavbildning ange mer än operativsystem eller ramverk. En basavbildning kan till exempel vara en avbildning av komponenten för delad tjänst som måste spåras. Medlemmar i ett team kan behöva spåra den här basavbildningen för testning eller behöver uppdatera avbildningen regelbundet när programavbildningar utvecklas.

## <a name="track-base-image-updates"></a>Spåra grundläggande bilduppdateringar

I ACR Tasks finns möjligheten att automatiskt skapa avbildningar när en containers basavbildning uppdateras.

ACR-uppgifter identifierar dynamiskt basavbildningsberoenden när en behållaravbildning skapas. Som ett resultat kan den identifiera när en programavbildnings basavbildning uppdateras. Med en förkonfigurerad bygguppgift kan ACR-uppgifter automatiskt återskapa alla programavbildningar som refererar till basavbildningen. Med den här automatiska identifieringen och ombyggnaden sparar ACR-uppgifter den tid och ansträngning som normalt krävs för att manuellt spåra och uppdatera varje programavbildning som refererar till den uppdaterade basavbildningen.

## <a name="base-image-locations"></a>Platser för basbilder

För bildversioner från en Dockerfile identifierar en ACR-uppgift beroenden på basavbildningar på följande platser:

* Samma Azure-behållarregister där aktiviteten körs
* Ett annat privat Azure-behållarregister i samma eller en annan region 
* En offentlig återdejt i Docker Hub 
* En offentlig repo i Microsoft Container Registry

Om basbilden som anges `FROM` i uttrycket finns på en av dessa platser lägger ACR-aktiviteten till en krok för att säkerställa att bilden återskapas när som helst dess bas uppdateras.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

* **Basavbildningar för programavbildningar** - För närvarande spårar en ACR-uppgift endast basavbildningsuppdateringar för program *(runtime)* bilder. Den spårar inte basavbildningsuppdateringar för mellanliggande *(buildtime)* bilder som används i dockerfiler i flera steg.  

* **Aktiverad som standard** - När du skapar en ACR-uppgift med kommandot [az acr-aktivitetsskapande][az-acr-task-create] *aktiveras* som standard aktiviteten för utlösare av en basavbildningsuppdatering. Det vill `base-image-trigger-enabled` än, egenskapen är inställd på Sant. Om du vill inaktivera det här beteendet i en aktivitet uppdaterar du egenskapen till False. Kör till exempel följande kommando för [uppdatering av az acr-aktivitet:][az-acr-task-update]

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Utlösare för att spåra samband** - Om du vill att en ACR-aktivitet ska kunna bestämma och spåra en behållaravbildningsberoenden - som innehåller basavbildningen - måste du först utlösa aktiviteten för att skapa avbildningen minst en **gång**. Utlösa aktiviteten manuellt med kommandot [az acr-aktivitetskörning.][az-acr-task-run]

* **Stabil tagg för basavbildning** - För att utlösa en uppgift vid basavbildningsuppdatering måste basavbildningen ha en *stabil* tagg, till exempel `node:9-alpine`. Den här taggningen är typisk för en basavbildning som uppdateras med OS- och ramkorrigeringar till en senaste stabil version. Om basavbildningen uppdateras med en ny versionstagg utlöses inte en aktivitet. Mer information om bildtaggning finns i [vägledningen för metodtips](container-registry-image-tag-version.md). 

* **Andra aktivitetsutlösare** - I en uppgift som utlöses av basavbildningsuppdateringar kan du också aktivera utlösare baserat på [ursprungskodsavstage](container-registry-tutorial-build-task.md) eller [ett schema](container-registry-tasks-scheduled.md). En basavbildningsuppdatering kan också utlösa en [aktivitet i flera steg](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Nästa steg

Se följande självstudier för scenarier för att automatisera programavbildningsversioner när en basavbildning har uppdaterats:

* [Automatisera behållaravbildningsversioner när en basavbildning uppdateras i samma register](container-registry-tutorial-base-image-update.md)

* [Automatisera behållaravbildningsversioner när en basavbildning uppdateras i ett annat register](container-registry-tutorial-base-image-update.md)


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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
