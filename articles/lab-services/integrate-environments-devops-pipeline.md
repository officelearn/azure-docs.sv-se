---
title: Integrera miljöer i Azure Pipelines i Azure DevTest Labs
description: Lär dig hur du integrerar Azure DevTest Labs-miljöer i dina Azure DevOps-pipelines (Continuous Integration) och CD-pipelines (Continuous Delivery).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169413"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrera miljöer i dina Azure DevOps CI/CD-pipelines
Du kan använda tillägget Azure DevTest Labs Tasks som är installerat i Azure DevOps Services (tidigare kallat Visual Studio Team Services) för att enkelt integrera din kontinuerliga integration (CI)/ cd-pipeline (continuous delivery) med Azure DevTest Labs. Dessa tillägg gör det enklare att snabbt distribuera en [miljö](devtest-lab-test-env.md) för en viss testaktivitet och sedan ta bort den när testet är klart. 

Den här artikeln visar hur du skapar och distribuerar en miljö och sedan tar bort miljön, allt i en komplett pipeline. Du utför vanligtvis var och en av dessa uppgifter individuellt i din egen anpassade pipeline för att distribuera build-test. De tillägg som används i den här artikeln är utöver dessa [skapa / ta bort DTL VM-uppgifter:](devtest-lab-integrate-ci-cd-vsts.md)

- Skapa en miljö
- Ta bort en miljö

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera din CI/CD-pipeline med Azure DevTest Labs installerar du Azure [DevTest Labs Tasks-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) från Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Skapa och konfigurera labbet för miljöer
I det här avsnittet beskrivs hur du skapar och konfigurerar ett labb där Azure-miljön ska distribueras till.

1. [Skapa ett labb](devtest-lab-create-lab.md) om du inte redan har ett. 
2. Konfigurera labbet och skapa en miljömall genom att följa instruktionerna från den här artikeln: [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
3. I det här exemplet kan du [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)använda en befintlig Azure Quickstart-mall .
4. Kopiera mappen **201-web-app-redis-cache-sql-database** till **mappen ArmTemplate** i databasen som konfigurerats i steg 2.

## <a name="create-a-release-definition"></a>Skapa en versionsdefinition
Så här skapar du versionsdefinitionen:

1.  På fliken **Versioner** i **hubben Skapa & Release**väljer du **plustecknet (+).**
2.  I fönstret **Skapa versionsdefinition** markerar du mallen **Tom** och väljer sedan **Nästa**.
3.  Välj **Välj senare**och välj sedan **Skapa** för att skapa en ny versionsdefinition med en standardmiljö och inga länkade artefakter.
4.  Om du vill öppna snabbmenyn markerar du **ellipsen (...) bredvid** miljönamnet i den nya versionsdefinitionen och väljer sedan **Konfigurera variabler**.
5.  Ange följande värden för de variabler som du använder i versionsdefinitionsuppgifterna i **fönstret Konfigurera -** miljö:
1.  För **administratörLogin**anger du inloggningsnamnet för SQL Administrator.
2.  För **administratörLoginPassword**anger du lösenordet som ska användas av SQL Administrator-inloggningen. Använd "hänglås"-ikonen för att dölja och säkra lösenordet.
3.  För **databasNamn**anger du NAMNET på SQL Database.
4.  Dessa variabler är specifika för exempelmiljöer, olika miljöer kan ha olika variabler.

## <a name="create-an-environment"></a>Skapa en miljö
Nästa steg i distributionen är att skapa den miljö som ska användas för utvecklings- eller testningsändamål.

1. Välj Lägg till **uppgifter**i versionsdefinitionen .
2. Lägg till en Azure DevTest Labs Create Environment-uppgift på fliken **Uppgifter.** Konfigurera uppgiften enligt följande:
    1. För **Azure RM-prenumeration**väljer du en anslutning i listan **Tillgängliga Azure-tjänstanslutningar** eller skapar en mer begränsad behörighetsanslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkten](/azure/devops/pipelines/library/service-endpoints).
2. För **Labbnamn**väljer du namnet på den instans som du skapade tidigare*.
3. För **databasnamn**väljer du den databas där Resource Manager-mallen (201) har flyttats till*.
4. För **Mallnamn**väljer du namnet på den miljö som du sparade i källkodsdatabasen*. 
5. **Labbnamn,** **databasnamn**och **mallnamn** är de egna representationerna av Azure-resurs-ID:n. Om du anger det egna namnet manuellt blir det fel, men du kan välja informationen om du vill använda listrutorna manuellt.
6. För **Miljönamn**anger du ett namn för att unikt identifiera miljöinstansen i labbet.  Det måste vara unikt i labbet.
7. **Parameterfilen** och **parametrarna**gör att anpassade parametrar kan skickas till miljön. Båda eller båda kan användas för att ange parametervärdena. I det här exemplet används avsnittet Parametrar. Använd namnen på de variabler som du har definierat i miljön, till exempel:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Information i miljömallen kan skickas igenom i utdataavsnittet i mallen. Markera **Skapa utdatavariabler baserat på miljömallens utdata** så att andra aktiviteter kan använda data. `$(Reference name.Output Name)`är mönstret att följa. Om referensnamnet till exempel var DTL och utdatanamnet i mallen var platsen skulle variabeln vara `$(DTL.location)`.

## <a name="delete-the-environment"></a>Ta bort miljön
Det sista steget är att ta bort miljön som du distribuerade i din Azure DevTest Labs-instans. Du tar vanligtvis bort miljön när du har kört de utgående aktiviteterna eller kört de tester som du behöver på de distribuerade resurserna.

I versionsdefinitionen väljer du **Lägg till uppgifter**och lägger sedan till en Azure **DevTest Labs Delete Environment-uppgift** på fliken **Distribuera.** Konfigurera den på följande sätt:

1. Information om hur du tar bort den virtuella datorn finns i [Azure DevTest Labs-uppgifter:](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
    1. För **Azure RM-prenumeration**väljer du en anslutning i listan **Tillgängliga Azure-tjänstanslutningar** eller skapar en mer begränsad behörighetsanslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkten](/azure/devops/pipelines/library/service-endpoints).
    2. För **Labbnamn**väljer du labbet där miljön finns.
    3. För **Miljönamn**anger du namnet på den miljö som ska tas bort.
2. Ange ett namn för versionsdefinitionen och spara det sedan.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 
- [Skapa miljöer med flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
- Snabbstart Resource Manager-mallar för DevTest Labs automatisering från [DevTest Labs GitHub-databasen](https://github.com/Azure/azure-quickstart-templates).
- [Vsts felsökningssida](/azure/devops/pipelines/troubleshooting)

