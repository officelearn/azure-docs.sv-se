---
title: Bas avbildnings uppdateringar – uppgifter
description: Lär dig mer om grundläggande avbildningar för program behållar avbildningar och om hur en bas avbildnings uppdatering kan utlösa en Azure Container Registry aktivitet.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617936"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Om bas avbildnings uppdateringar för ACR-uppgifter

Den här artikeln innehåller bakgrunds information om uppdateringar av ett programs bas avbildning och hur dessa uppdateringar kan utlösa en Azure Container Registry aktivitet.

## <a name="what-are-base-images"></a>Vad är bas avbildningar?

Dockerfiles som definierar de flesta behållar avbildningar anger en överordnad avbildning som avbildningen är baserad på, vilket ofta kallas för *bas avbildningen*. Basavbildningar innehåller vanligtvis operativsystemet, till exempel [Alpine Linux][base-alpine] eller [Windows Nano Server][base-windows], där resten av containerns lager tillämpas. De kan även innehålla programramverk som [Node.js][base-node] eller [.NET Core][base-dotnet]. De här bas avbildningarna är vanligt vis baserade på offentliga överordnade avbildningar. Flera av dina program avbildningar kan dela en vanlig bas avbildning.

En basavbildning uppdateras ofta av avbildningens underhållare med nya funktioner och förbättringar av operativsystem eller ramverk i avbildningen. Säkerhetskorrigeringar är en annan vanlig orsak till att en basavbildning uppdateras. När dessa överordnade uppdateringar inträffar måste du också uppdatera dina bas avbildningar för att inkludera den kritiska korrigeringen. Varje program avbildning måste sedan återskapas för att inkludera dessa uppströms korrigeringar som nu ingår i bas avbildningen.

I vissa fall, till exempel ett privat utvecklings team, kan en bas avbildning ange mer än operativ system eller ramverk. En bas avbildning kan till exempel vara en avbildning av en delad tjänst komponent som måste spåras. Medlemmar i ett team kan behöva spåra den här bas avbildningen för testning eller behöver regelbundet uppdatera avbildningen när de utvecklar program avbildningar.

## <a name="track-base-image-updates"></a>Spåra bas avbildnings uppdateringar

I ACR Tasks finns möjligheten att automatiskt skapa avbildningar när en containers basavbildning uppdateras.

ACR-aktiviteter identifierar dynamiskt bas avbildnings beroenden när en behållar avbildning skapas. Det innebär att den kan identifiera när en program avbildnings bas avbildning uppdateras. Med en förkonfigurerad versions uppgift kan ACR-aktiviteter automatiskt återskapa varje program avbildning som refererar till bas avbildningen. Med den här automatiska identifieringen och återuppbyggnaden sparar ACR-uppgifter den tid och ansträngning som normalt krävs för att manuellt spåra och uppdatera varje program avbildning som refererar till den uppdaterade bas avbildningen.

## <a name="base-image-locations"></a>Bas avbildnings platser

För avbildningar som bygger på en Dockerfile identifierar en ACR-uppgift beroenden för bas avbildningarna på följande platser:

* Samma Azure Container Registry där aktiviteten körs
* Ett annat privat Azure Container Registry i samma eller en annan region 
* En offentlig lagrings platsen i Docker Hub 
* En offentlig lagrings platsen i Microsoft Container Registry

Om bas avbildningen som anges i `FROM` instruktionen finns på någon av dessa platser lägger ACR-aktiviteten till en Hook för att se till att avbildningen återskapas när dess bas uppdateras.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

* **Bas avbildningar för program avbildningar** – för närvarande spårar en ACR-uppgift endast bas avbildnings uppdateringar för program (*runtime*)-avbildningar. Det spårar inte bas avbildnings uppdateringar för mellanliggande (*buildtime*) avbildningar som används i Dockerfiles med flera steg.  

* **Aktive rad som standard** – när du skapar en ACR-uppgift med kommandot [AZ ACR Task Create][az-acr-task-create] , *aktive* ras uppgiften som standard för Utlös ande av en bas avbildnings uppdatering. Det vill säga `base-image-trigger-enabled` egenskapen anges till sant. Om du vill inaktivera det här beteendet i en aktivitet uppdaterar du egenskapen till false. Kör till exempel följande [AZ ACR aktivitets uppdaterings][az-acr-task-update] kommando:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Utlös för att spåra beroenden** – om du vill aktivera en ACR-uppgift för att fastställa och spåra en behållar avbildnings beroenden – som inkluderar dess bas avbildning, måste du först utlösa uppgiften för att skapa avbildningen **minst en gång**. Utlös till exempel uppgiften manuellt med kommandot [AZ ACR Task Run][az-acr-task-run] .

* **Stabil tagg för bas avbildning** – för att utlösa en aktivitet på bas avbildnings uppdatering måste bas avbildningen ha en *stabil* tagg, till `node:9-alpine`exempel. Den här märkningen är typisk för en bas avbildning som uppdateras med OS-och Framework-korrigeringsfiler till en senaste stabil utgåva. Om bas avbildningen uppdateras med en ny versions tagg utlöses inte en uppgift. Mer information om bild taggning finns i [rikt linjer för bästa praxis](container-registry-image-tag-version.md). 

* **Andra aktivitets utlösare** – i en aktivitet som utlöses av bas avbildnings uppdateringar kan du också aktivera utlösare baserat på [käll kods genomförande](container-registry-tutorial-build-task.md) eller [ett schema](container-registry-tasks-scheduled.md). En bas avbildnings uppdatering kan även utlösa en [aktivitet i flera steg](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Nästa steg

Se följande självstudier för att automatisera program avbildnings versioner när en bas avbildning har uppdaterats:

* [Automatisera behållar avbildningen skapar när en bas avbildning uppdateras i samma register](container-registry-tutorial-base-image-update.md)

* [Automatisera behållar avbildningen skapar när en bas avbildning uppdateras i ett annat register](container-registry-tutorial-base-image-update.md)


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
