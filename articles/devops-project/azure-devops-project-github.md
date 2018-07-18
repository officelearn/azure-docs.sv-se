---
title: Skapa en CI/CD-pipeline för din befintliga kod med Azure DevOps-projekt | VSTS-självstudie
description: DevOps-projekt gör det enkelt att komma igång med Azure. Det hjälper dig att använda egen kod och din GitHub-lagringsplats för att starta en app på en Azure-tjänst med några enkla få steg.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 8c92b45cd3949e56515286c963b035e3c449835b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967449"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Skapa en CI/CD-pipeline för din befintliga kod med Azure DevOps-projektet

Azure DevOps-projektet ger ett förenklat sätt som gör att du kan ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt
> * Konfigurera åtkomst till din GitHub-lagringsplats och välj ett ramverk
> * Konfigurera VSTS och en Azure-prenumeration 
> * Genomför ändringar i GitHub och distribuera automatiskt till Azure
> * Granska VSTS-CI/CD-pipelinen
> * Konfigurera övervakning med Azure Application Insights

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Åtkomst till en GitHub eller en extern Git-lagringsplats som innehåller .NET, Java, PHP, Node, Python eller statisk webbkod.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps-projektet skapar en CI/CD-pipeline i VSTS.  Du kan skapa ett **nytt VSTS**-konto eller använda ett **befintligt konto**.  Azure DevOps-projektet skapar även **Azure-resurser** i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **+ Ny** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **Ha med din egen kod**.  När du är klar väljer du **Nästa**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Konfigurera åtkomst till din GitHub-lagringsplats och välj ett ramverk

1. Välj **GitHub**.  Om du vill kan du välja en **extern Git-lagringsplats**.  Välj din **lagringsplats** och **gren** som innehåller programmet.

1. Välj ditt **webbramverk**.  När du är klar väljer du **Nästa**.

    ![.NET-ramverk](_img/azure-devops-project-github/webframework.png)

1. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här.  Välj **target service** (måltjänsten) du önskar.  När du är klar väljer du **Nästa**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurera VSTS och en Azure-prenumeration 

1. Skapa ett **nytt** VSTS-konto eller välj ett **befintligt** konto.  Välj ett **namn** för ditt VSTS-projekt.  Välj din **Azure-prenumeration**, **plats** och välj ett **namn** för ditt program.  När du är klar väljer du **Klar**.

    ![Ange VSTS-information](_img/azure-devops-project-github/vstsazureinfo.png)

1. **Projektinstrumentpanelen** läses in i Azure-portalen på några minuter.  Ett exempelprogram konfigureras i en lagringsplats i VSTS-kontot, en version körs och programmet distribueras till Azure.  Den här instrumentpanelen ger insyn i din **kodlagringsplats** på GitHub, **VSTS CI/CD-pipeline** och i ditt **program i Azure**.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa programmet som körs.

    ![Instrumentpanelsvy](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps-projektet konfigurerar automatiskt en CI-version och släpper utlösaren.  Koden finns kvar i din GitHub eller annan extern lagringsplats.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Genomför ändringar i GitHub och distribuera automatiskt till Azure 

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring i GitHub-lagringsplatsen startar en version i VSTS och en VSTS Release Management-definition kör en distribution till Azure.

1.  Gör en ändring i ditt program och **genomför** ändringen till GitHub-lagringsplatsen.
2.  Efter en liten stund startar en version i VSTS.  Du kan övervaka versionstillståndet med instrumentpanelen för DevOps-projektet eller i webbläsaren med ditt VSTS-konto.
3.  När versionen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="examine-the-vsts-cicd-pipeline"></a>Granska VSTS-CI/CD-pipelinen

Azure DevOps-projektet konfigurerade automatiskt en fullständig VSTS-CI/CD-pipeline i VSTS-kontot.  Utforska och anpassa pipelinen efter behov.  Följ stegen nedan för att bekanta dig med VSTS-versionen och versionsdefinitioner.

1. Välj **Skapa pipelines** från **längst upp** på instrumentpanelen för Azure DevOps-projektet.  Den här länken öppnar en flik i webbläsaren och öppnar VSTS versionsdefinition för det nya projektet.

1. Flytta markören till höger om versionsdefinition bredvid fältet **Status**. Välj **ellipsen** som visas.  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel att lägga till en ny version i en kö, pausa en version och redigera versionsdefinitionen.

1. Välj **Redigera**.

1. **Granska de olika uppgifterna** för versionsdefinition från den här vyn.  Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Längst upp i versionsdefinitionen väljer du **build definition name** (namn på versionsdefinitionen).

1. Ändra **namnet** på din versionsdefinition till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **spara**.

1. Under ditt versionsdefinitionsnamn väljer du **historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  VSTS spårar alla ändringar som görs på versionsdefinition vilket gör att du kan jämföra versioner.

1. Välj **utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **kvarhållning**.  Du kan ange principer för att behålla eller ta bort ett visst antal versioner baserat på ditt scenario.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en VSTS-versionsdefinition för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionsdefinition och sedan väljer du **redigera**.

1. Versionsdefinitionen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den versionsdefinition du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  Den här versionsdefinitionen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. På vänster sida i webbläsaren väljer du **Uppgifter**.  Uppgifter är de aktiviteter som distributionsprocessen utför.  I det här exemplet skapades en uppgift för att distribuera till **Azure App-tjänsten**.

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurera övervakning med Azure Application Insights

Med Azure Application Insights kan du enkelt övervaka ett programs prestanda och användning.  Azure DevOps-projektet konfigurerade automatiskt en Application Insights-resurs för ditt program.  Du kan konfigurera ytterligare aviseringar och övervakningsfunktioner efter behov.

1. I Microsoft Azure-portalen navigerar du till instrumentpanelen för **Azure DevOps-projektet**.  Längst ned till höger på instrumentpanelen väljer du länken **Application Insights** för din app.

1. Bladet **Application Insights** öppnas på Microsoft Azure-portalen.  Den här vyn innehåller övervakningsinformation om användning, prestanda och tillgänglighet för din app.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Välj **Tidsintervall** och sedan **Senaste timmen**.  Välj **Uppdatera** för att filtrera resultaten.  Nu ser du alla aktiviteter från de senaste 60 minuterna.  Välj **x** för att avsluta inställningen av tidsintervall.

1. Välj **Aviseringar** och sedan **+ Lägg till metrisk varning**.  

1. Ange ett **namn** för aviseringen.

1. Välj listrutan för **Source Alter on** (Ändring av källa: På).  Välj din **App Service-resurs**.

1. Standardaviseringen är för en **serversvarstid som är större än 1 sekund**.  Välj listrutan **Statistik** för att undersöka statistik om aviseringar.  Du kan enkelt konfigurera en mängd olika aviseringar för att förbättra övervakningsfunktionerna i din app.

1. Välj kryssrutan för att **meddela e-postägare, deltagare och läsare**.  Alternativt kan du utföra ytterligare åtgärder när en avisering utlöses genom att köra Azure-logikapp.

1. Välj **OK** för att skapa aviseringen.  Efter en liten stund visas aviseringen som aktiv på instrumentpanelen.  **Avsluta** området för aviseringar och gå tillbaka till **Application Insights-bladet**.

1. Välj **Tillgänglighet**och sedan **+ Lägg till test**. 

1. Ange ett **Testnamn** och välj sedan **Skapa**.  Ett enkelt pingtest skapas för att kontrollera tillgängligheten för ditt program.  Testresultaten blir tillgängliga efter några minuter och Application Insights-instrumentpanelen visar en tillgänglighetsstatus.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App-tjänsten och relaterade resurser som skapats i den här snabbstarten med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen i den här självstudien skapades automatiskt en build-versions- och en versionsdefinition i VSTS-projektet. Du kan ändra dessa build-versions- och versionsdefinitioner för att uppfylla behoven i ditt team. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt
> * Konfigurera åtkomst till din GitHub-lagringsplats och välj ett ramverk
> * Konfigurera VSTS och en Azure-prenumeration 
> * Genomför ändringar i GitHub och distribuera automatiskt till Azure
> * Granska VSTS-CI/CD-pipelinen
> * Konfigurera övervakning med Azure Application Insights

Mer information om VSTS-pipeline finns i den här självstudien:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
