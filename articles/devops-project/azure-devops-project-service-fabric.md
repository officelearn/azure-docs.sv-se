---
title: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt | VSTS självstudier
description: DevOps-projekt gör det enkelt att komma igång med Azure. Azure DevOps-projekt gör det enkelt att distribuera din ASP.NET Core-app med Azure Service Fabric i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2fd1fc968eb6b61d7378dbc0efa48f2f7eb2aa54
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967423"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Självstudier: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt | VSTS självstudier

Azure DevOps-projektet ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.  DevOps-projektet skapar automatiskt Azure-resurser som Azure Service Fabric, skapar och konfigurerar en versionspipeline i VSTS som innehåller en definition av build för CI, ställer in en versionsdefinition för CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI versionsdefinition
> * Granska VSTS CD Release versionshantering
> * Checka in ändringar av VSTS och automatiskt distribuera till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfri [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric

Azure DevOps-projektet skapar en CI/CD-pipeline i VSTS.  Du kan skapa ett **nytt VSTS**-konto eller använda ett **befintligt konto**.  Azure DevOps-projektet skapar även **Azure-resurser** som ett Service Fabric-kluster i den **Azure-prenumeration** som du väljer.

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **Skapa en resurs** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Välj **.NET** och välj sedan **Nästa**.

1. För **Välj ett programramverk** väljer du **ASP.NET Core**. Välj sedan **Nästa**.

1. Välj **Service Fabric-kluster** och sedan **Nästa**.  

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurera VSTS och en Azure-prenumeration

1. Skapa ett **nytt** VSTS-konto eller välj ett **befintligt** konto.  Välj ett **namn** för ditt VSTS-projekt.  

1. Välj din **Azure-prenumeration**.

1. Välj länken **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera **nodstorleken för den virtuella datorn** och **operativsystemet** för **Service Fabric-klustret**.  Det finns flera olika alternativ här för konfigurering av typ och placering av Azure-tjänster.
 
1. Avsluta konfiguration av Azure-området och välj **Klar**.

1. Det tar flera minuter för processen att slutföras.  Ett exempelprogram för ASP.NET Core konfigureras i en lagringsplats i VSTS-kontot, ett Service Fabric-kluster skapas, en version och release körs och programmet distribueras till Azure.  

    När det är klart läses Azure DevOps **projektinstrumentpanel** in i Azure-portalen.  Du kan också navigera till **instrumentpanelen för Azure DevOps-projektet** direkt från **Alla resurser** i **Azure-portalen**.  

    Den här instrumentpanelen ger insyn i din VSTS **kodlagringsplats**, **VSTS CI/CD-pipeline** och i ditt **Service Fabric-kluster**.  Du kan konfigurera fler alternativ i VSTS.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa programmet som körs.

## <a name="examine-the-vsts-ci-build-definition"></a>Granska VSTS CI versionsdefinition

Azure DevOps-projektet konfigurerade automatiskt en fullständig VSTS-CI/CD-pipeline i VSTS-kontot.  Du kan utforska och anpassa pipelinen.  Följ stegen nedan för att bekanta dig med VSTS-versionsdefinitionen.

1. Navigerar till **instrumentpanelen för Azure DevOps-projektet**.

1. Välj **Skapa pipelines** **längst upp** på **instrumentpanelen för Azure DevOps-projektet**.  Den här länken öppnar en flik i webbläsaren och öppnar VSTS versionsdefinition för det nya projektet.

1. Flytta markören till höger om versionsdefinition bredvid fältet **Status**. Välj **ellipsen** som visas.  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel att **lägga till en ny version i en kö**, **pausa en version** och **redigera versionsdefinitionen**.

1. Välj **Redigera**.

1. **Granska de olika uppgifterna** för versionsdefinition från den här vyn.  Versionen kör olika uppgifter som att hämta källor från VSTS Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Längst upp i versionsdefinitionen väljer du **namnet på versionsdefinitionen**.

1. Ändra **namnet** på din versionsdefinition till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **Spara**.

1. Under ditt versionsdefinitionsnamn väljer du **Historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  VSTS spårar alla ändringar som görs på versionsdefinition vilket gör att du kan jämföra versioner.

1. Välj **Utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  Du kan ange principer för att behålla eller ta bort ett visst antal versioner baserat på ditt scenario.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Granska VSTS CD Release versionshantering

Azure DevOps-projekt skapar och konfigurerar automatiskt nödvändiga åtgärder för att distribuera från ditt VSTS-konto på din Azure-prenumerationen.  De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera VSTS till din Azure-prenumeration.  Automationen skapar också en VSTS-versionsdefinition och den versionen ger CD:n till Azure.  Följ stegen nedan för att få veta mer om VSTS-versionsdefinitionen.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en VSTS-versionsdefinition för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionsdefinition och sedan väljer du **redigera**.

1. Versionsdefinitionen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den versionsdefinition du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **ikonen** **Utlösare av kontinuerlig distribution** (som ser ut som en blixt).  Den här versionen har en aktiverad CD-utlösare.  Utlösaren startar en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en **versionssammanfattning**, **tillhörande arbetsobjekt** och **tester**.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. Du kan jämföra versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Checka in ändringar av VSTS och automatiskt distribuera till Azure 

 > [!NOTE]
 > Stegen nedan testar CI/CD-pipeline med en enkel textändring för din webbapp.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring i VSTS-git-lagringsplatsen startar en version i VSTS och en VSTS Release Management-definition distribuerar dina ändringar till Azure.  Följ stegen nedan eller använd andra metoder för att göra ändringar till databasen.  Du kan till exempel **klona** Git-lagringsplatsen i ditt favoritverktyget eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. Välj **Kod** och sedan **Filer** från VSTS-menyn och navigera till din lagringsplats.
1. Navigera till katalogen **Views\Home** och välj **ellipsen** bredvid filen **Index.cshtml**. Välj sedan **redigera** .

1. Gör en ändring i filen, till exempel i texten i en av **div-taggarna**.  Välj **Genomför** i det övre högra hörnet.  Välj **Genomför** igen för att push-överföra ändringen. 

1. Efter en stund **startar en version i VSTS** och därefter körs en version för att distribuera ändringarna.  Du kan övervaka **build status** (versionstillståndet) med instrumentpanelen för DevOps-projektet eller i webbläsaren med ditt VSTS-konto.

1. När lanseringen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

 > [!NOTE]
 > Stegen nedan tar permanent bort resurser.  Använd endast den här funktionen efter att ha läst anvisningarna noga.

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter.  När du inte längre behöver klustret kan du ta bort Azure Service Fabric-klustret och relaterade resurser som skapats i den här självstudien med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projekt.  **Var försiktig** eftersom funktionen Ta bort förstör alla data som skapats av Azure DevOps-projektet i både Azure och VSTS. Du kommer inte att kunna återskapa dem när de har tagits bort.

1. Från **Azure-portalen**, gå till **Azure DevOps-projekt**.
2. I **övre högra hörnet** av instrumentpanelen väljer du **Ta bort**.  När du har läst dialogrutan väljer du **Ja** för att **permanent ta bort** resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa build-versions- och versionsdefinitioner för att uppfylla behoven i ditt team. Du kan också använda det här CI/CD-mönstret som en mall för dina andra projekt.  Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI versionsdefinition
> * Granska VSTS CD Release versionshantering
> * Checka in ändringar av VSTS och automatiskt distribuera till Azure
> * Rensa resurser

Om du vill veta nedan mer om Service Fabric och mikrotjänster:

> [!div class="nextstepaction"]
> [Använda mikrotjänster för att bygga program](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
