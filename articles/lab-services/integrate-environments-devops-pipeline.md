---
title: Integrera miljöer i Azure-Pipelines i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du integrerar Azure DevTest Labs-miljöer i din Azure DevOps kontinuerlig integrering (CI) och pipelines för kontinuerlig leverans (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357402"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrera miljöer i din Azure DevOps CI/CD-pipelines
Du kan använda tillägget Azure DevTest Labs-uppgifter som är installerad i Azure DevOps-tjänsterna (kallades tidigare Visual Studio Team Services) för att enkelt integrera din kontinuerlig integrering (CI) / kontinuerlig leverans (CD) version och versionen pipeline med Azure DevTest Labs. De här tilläggen gör det lättare att snabbt distribuera en [miljö](devtest-lab-test-env.md) för en specifik testa aktivitet och tar bort den när testet är klart. 

Den här artikeln visar hur du skapar och distribuerar en miljö och sedan ta bort miljön, allt i en fullständig pipeline. Normalt utför var och en av dessa uppgifter individuellt i din egen anpassade build-test-distribuera pipeline: Tillägg som används i den här artikeln gäller utöver dessa [skapa/ta bort DTL VM uppgifter](devtest-lab-integrate-ci-cd-vsts.md):

- Skapa en miljö
- Ta bort en miljö

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera dina CI/CD-pipeline med Azure DevTest Labs, installera [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) tillägget från Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Skapa och konfigurera labb för miljöer
Det här avsnittet beskriver hur du skapar och konfigurerar ett labb där Azure-miljön ska distribueras till.

1. [Skapa ett labb](devtest-lab-create-lab.md) om du inte redan har ett. 
2. Konfigurera labbet och skapa en mall för miljön genom att följa instruktionerna från den här artikeln: [Skapa miljöer för flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
3. Det här exemplet använder en befintlig Azure-Snabbstartsmall [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Kopiera den **201-web-app-redis-cache-sql-database** mapp till den **ArmTemplate** mapp i databasen som är konfigurerade i steg 2.

## <a name="create-a-release-definition"></a>Skapa en versionsdefinition
Om du vill skapa versionsdefinitionen, gör du följande:

1.  På den **versioner** fliken den **version och utgåva hub**väljer den **plustecknet (+)** knappen.
2.  I den **skapa versionsdefinition** väljer den **tom** mallen och välj sedan **nästa**.
3.  Välj **väljer senare**, och välj sedan **skapa** att skapa en ny versionsdefinition med en standardmiljö och inga länkade artefakter.
4.  För att öppna snabbmenyn i nya versionsdefinitionen, Välj den **ellipsen (...)**  Nästa om du vill miljönamnet och välj sedan **konfigurera variabler**.
5.  I den **konfigurera - miljö** fönster för de variabler som du använder i definitionen publiceringsuppgifter anger du följande värden:
1.  För **administratorLogin**, ange inloggningsnamnet SQL-administratören.
2.  För **administratorLoginPassword**, ange lösenordet som ska användas vid inloggningen för SQL-administratören. Använd hänglåsikonen ”” för att dölja och skydda lösenordet.
3.  För **databaseName**, ange namnet på SQL-databasen.
4.  Dessa variabler är specifika för exempelmiljöer, olika miljöer kan ha olika variabler.

## <a name="create-an-environment"></a>Skapa en miljö
Nästa steg i distributionen är att skapa miljön som ska användas för utveckling eller testning.

1. Välj i versionsdefinitionen, **lägga till aktiviteter**.
2. På den **uppgifter** fliken, Lägg till en uppgift i Azure DevTest Labs skapa miljö. Konfigurera aktiviteten enligt följande:
    1. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](/azure/devops/pipelines/library/service-endpoints).
2. För **Labbnamn**, Välj namnet på den instans som du skapade tidigare *.
3. För **databasnamn**, Välj den databas där Resource Manager-mall (201) har överförts till *.
4. För **mallnamn**, Välj namnet på den miljö som du sparade till din datakälla kod lagringsplats *. 
5. Den **Labbnamn**, **Lagringsplatsnamn**, och **mallnamn** är eget representationer av Azure-resursen ID: N. Att manuellt ange det egna namnet orsaka fel, använder de nedrullningsbara listorna för att välja information.
6. För **Miljönamn**, ange ett namn som unikt identifierar miljö-instansen i labbet.  Det måste vara unikt i labbet.
7. Den **parameterfilen** och **parametrar**, Tillåt anpassade parametrar som ska skickas till miljön. Ett eller båda kan användas för att ange parametervärden. I det här exemplet används parameteravsnittet. Använda namnen på de variabler som du har definierat i miljön, till exempel: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Information i mallen miljö kan skickas via i utdata-avsnitt i mallen. Kontrollera **skapa utdata variabler baserat på miljön mallutdata** så att andra aktiviteter kan använda data. `$(Reference name.Output Name)` är mönstret för att följa. Till exempel om referensnamnet var DTL och utdatanamn i mallen plats variabeln blir `$(DTL.location)`.

## <a name="delete-the-environment"></a>Ta bort miljön
Det sista steget är att ta bort miljön som du distribuerade i din Azure DevTest Labs-instans. Du skulle normalt ta bort miljön när du kör uppgifterna för utveckling eller köra testerna som du behöver på distribuerade resurser.

I versionsdefinitionen, väljer **lägga till aktiviteter**, och klicka sedan på den **distribuera** fliken, lägga till en **ta bort Azure DevTest Labs-miljön** uppgift. Konfigurera enligt följande:

1. Om du vill ta bort den virtuella datorn, se [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](/azure/devops/pipelines/library/service-endpoints).
    2. För **Labbnamn**, Välj labbet där miljön finns.
    3. För **Miljönamn**, anger du namnet på miljön som ska tas bort.
2. Ange ett namn för versionsdefinitionen och spara den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 
- [Skapa miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
- Snabbstart för Resource Manager-mallar för DevTest Labs automation från den [DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates).
- [Felsökning av VSTS-sidan](/azure/devops/pipelines/troubleshooting)

