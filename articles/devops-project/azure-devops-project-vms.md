---
title: Distribuera din ASP.NET-app till Azure Virtual Machines med Azure DevOps-projekt | VSTS-självstudier
description: DevOps-projekt gör det enkelt att komma igång med Azure. Azure DevOps-projekt gör det enkelt att distribuera din ASP.NET-app till Azure Virtual Machines i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b5a9ce204f68d812da4dfcfebb18b79b9c70c6c9
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967444"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Självstudie: Distribuera din ASP.NET-app till Azure Virtual Machines med Azure DevOps-projekt

Azure DevOps-projektet ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.  DevOps-projektet skapar automatiskt Azure-resurser som en ny virtuell Azure-dator, skapar och konfigurerar en versionspipeline i VSTS som innehåller en versionsdefinition för CI, ställer in en versionsdefinition för CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET-app
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI-versionsdefinitionen
> * Granska VSTS CD Release Management-definitionen
> * Genomför ändringar av VSTS och distribuera automatiskt till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Skapa ett Azure DevOps-projekt för en ASP.NET-app

Azure DevOps-projektet skapar en CI/CD-pipeline i VSTS.  Du kan skapa ett **nytt VSTS**-konto eller använda ett **befintligt konto**.  Azure DevOps-projektet skapar även **Azure-resurser** som virtuella datorer i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **+ Ny** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **.NET** och välj sedan **Nästa**.

1. För **Välj ett programramverk** väljer du **ASP.NET**. Välj sedan **Nästa**. 

1. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här.  Välj den **virtuella datorn** och välj sedan **Nästa**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurera VSTS och en Azure-prenumeration

1. Skapa ett **nytt** VSTS-konto eller välj ett **befintligt** konto.  Välj ett **namn** för ditt VSTS-projekt.  

1. Välj din **Azure-prenumeration**.  Du kan också välja länken **Ändra** och ange sedan mer konfigurationsinformation, till exempel ändra platsen för Azure-resurser.
 
1. Ange ett **Namn på virtuell dator**, **Användarnamn** och **Lösenord** för din nya virtuella Azure-datorresurs och välj sedan **Klar**.

1. Det tar några minuter innan den virtuella Azure-datorn är klar.  Ett exempelprogram för ASP.NET konfigureras i en lagringsplats i VSTS-kontot, en version och release körs och programmet distribueras till den nyskapade virtuella Azure-datorn.  

    När det är klart läses Azure DevOps **projektinstrumentpanel** in i Azure-portalen.  Du kan också navigera till **instrumentpanelen för Azure DevOps-projektet** direkt från **Alla resurser** i **Azure-portalen**.  

    Den här instrumentpanelen ger insyn i **kodlagringsplatsen** för VSTS, **VSTS CI/CD-pipeline** och i det **program i Azure** som körs.    

    ![Instrumentpanelsvy](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Azure DevOps-projektet konfigurerar automatiskt en CI-version och släpper utlösaren som automatiskt distribuerar kodändringar till din lagringsplats.  Du kan konfigurera fler alternativ i VSTS.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa programmet som körs.
    
## <a name="examine-the-vsts-ci-build-definition"></a>Granska VSTS CI-versionsdefinitionen

Azure DevOps-projektet konfigurerar automatiskt en fullständig VSTS-CI/CD-pipeline i VSTS-kontot.  Du kan utforska och anpassa pipelinen.  Följ stegen nedan för att bekanta dig med VSTS-versionsdefinitionen.

1. Välj **Skapa pipelines** **längst upp** på **instrumentpanelen för Azure DevOps-projektet**.  Den här länken öppnar en flik i webbläsaren och öppnar VSTS versionsdefinition för det nya projektet.

1. Flytta markören till höger om versionsdefinition bredvid fältet **Status**. Välj **ellipsen** som visas.  Den här åtgärden öppnar en meny där du kan utföra flera aktiviteter, till exempel att **lägga till en ny version i en kö**, **pausa en version** och **redigera versionsdefinitionen**.

1. Välj **Redigera**.

1. **Granska de olika uppgifterna** för versionsdefinition från den här vyn.  Versionen kör olika uppgifter som att hämta källor från VSTS Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Längst upp i versionsdefinitionen väljer du **build definition name** (namn på versionsdefinitionen).

1. Ändra **namnet** på din versionsdefinition till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **spara**.

1. Under ditt versionsdefinitionsnamn väljer du **historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  VSTS spårar alla ändringar som görs på versionsdefinition vilket gör att du kan jämföra versioner.

1. Välj **utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare och varje incheckning till lagringsplatsen startar en ny version.  Alternativt kan du inkludera eller exkludera grenar från CI-processen.

1. Välj **kvarhållning**.  Du kan ange principer för att behålla eller ta bort ett visst antal versioner baserat på ditt scenario.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Granska VSTS CD Release Management-definitionen

Azure DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att distribuera från ditt VSTS-konto på Azure-prenumerationen automatiskt.  De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera VSTS till din Azure-prenumeration.  Automationen skapar också en VSTS-versionsdefinition och den tillhandahåller CD:n för den virtuella Azure-datorn.  Följ stegen nedan för att få veta mer om VSTS-versionsdefinitionen.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en VSTS-versionsdefinition för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionsdefinition och sedan väljer du **redigera**.

1. Versionsdefinitionen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den versionsdefinition du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **ikonen** **Utlösare av kontinuerlig distribution** (som ser ut som en blixt).  Den här versionen har en aktiverad CD-utlösare.  Utlösaren startar en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. På vänster sida i webbläsaren väljer du **Uppgifter** och sedan väljer du **miljö**.  

1. Uppgifter är de aktiviteter som distributionsprocessen kör och de grupperas ihop i **Faser**.  Det finns två **faser** för den här versionsdefinitionen.  Den första fasen innehåller en uppgift för **Azure Resource Group Deployment** (Distribution av Azure-resursgrupp) som konfigurerar den virtuella datorn för distribution och lägger till den nya virtuella datorn till en **VSTS Deployment Group** (VSTS-distributionsgrupp).  VM-distributionsgruppen i VSTS hanterar logiska grupper för datorer som är **distributionsmål**.

1. I den andra fasen skapades en **IIS Web App Manage**-uppgift för att skapa en IIS-webbplats på den virtuella datorn.  En andra **IIS Web App Deploy**-uppgift skapades för att distribuera platsen.

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en **versionssammanfattning**, **tillhörande arbetsobjekt** och **tester**.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Genomför ändringar av VSTS och distribuera automatiskt till Azure 

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring i VSTS-git-lagringsplatsen startar en version i VSTS och en VSTS Release Management-definition kör en distribution till din virtuella Azure-dator.  Följ stegen nedan eller använd andra metoder för att göra ändringar till databasen.  Kodändringarna startar CI/CD-processen och distribuerar automatiskt dina ändringar till IIS-webbplatsen på den virtuella Azure-datorn.

1. Välj **kod** från VSTS-menyn och navigera till din lagringsplats.

1. Navigera till katalogen **Views\Home** och välj **ellipsen** bredvid filen **Index.cshtml**. Välj sedan **redigera** .

1. Gör en ändring i filen, till exempel i texten i en av **div-taggarna**.  Välj **Genomför** i det övre högra hörnet.  Välj **Genomför** igen för att push-överföra ändringen. 

1. Efter en stund **startar en version i VSTS** och därefter körs en version för att distribuera ändringarna.  Övervaka **build status** (versionstillståndet) med instrumentpanelen för DevOps-projektet eller i webbläsaren med ditt VSTS-konto.

1. När lanseringen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurera övervakning med Azure Application Insights

Med Azure Application Insights kan du enkelt övervaka ett programs prestanda och användning.  Azure DevOps-projektet konfigurerade automatiskt en Application Insights-resurs för ditt program.  Du kan konfigurera ytterligare aviseringar och övervakningsfunktioner efter behov.

1. I **Microsoft Azure-portalen** navigerar du till instrumentpanelen för **Azure DevOps-projektet**.  Längst ned till höger på instrumentpanelen väljer du länken **Application Insights** för din app.

1. Bladet **Application Insights** öppnas på Microsoft Azure-portalen.  Den här vyn innehåller övervakningsinformation om användning, prestanda och tillgänglighet för din app.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Välj **Tidsintervall** och sedan **Senaste timmen**.  Välj **Uppdatera** för att filtrera resultaten.  Nu ser du alla aktiviteter från de senaste 60 minuterna.  Välj **x** för att avsluta inställningen av tidsintervall.

1. Längst upp på instrumentpanelen finns **Aviseringar** och flera andra användbara länkar.  Välj **Aviseringar** och sedan **+ Lägg till metrisk varning**.

1. Ange ett **namn** för aviseringen.

1. Standardaviseringen är för en **serversvarstid som är större än 1 sekund**.  Välj listrutan **Statistik** för att undersöka statistik om aviseringar.  Du kan till exempel konfigurera **Körningstid för begäran för ASP.NET** för en ASP.NET-app.  Du kan enkelt konfigurera en mängd olika aviseringar för att förbättra övervakningsfunktionerna i din app.

1. Välj kryssrutan för att **meddela e-postägare, deltagare och läsare**.  Alternativt kan du utföra ytterligare åtgärder när en avisering utlöses genom att köra Azure-logikapp.

1. Välj **OK** för att skapa aviseringen.  Efter en liten stund visas aviseringen som aktiv på instrumentpanelen.  **Avsluta** området för aviseringar och gå tillbaka till **Application Insights-bladet**.

1. Välj **Tillgänglighet**och sedan **+ Lägg till test**. 

1. Ange ett **Testnamn** och välj sedan **Skapa**.  Det här skapar ett enkelt pingtest för att kontrollera tillgängligheten för ditt program.  Testresultaten blir tillgängliga efter några minuter och Application Insights-instrumentpanelen visar en tillgänglighetsstatus.

## <a name="clean-up-resources"></a>Rensa resurser

 > [!NOTE]
 > Stegen nedan tar permanent bort resurser.  Använd endast den här funktionen efter att ha läst anvisningarna noga.

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter.  När du inte längre behöver det kan du ta bort den virtuella Azure-datorn och relaterade resurser som skapats i den här självstudien med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projekt.  **Var försiktig** eftersom funktionen Ta bort förstör alla data som skapats av Azure DevOps-projektet i både Azure och VSTS. Du kommer inte att kunna återskapa dem när de har tagits bort.

1. Gå till **Azure DevOps-projekt** från **Azure-portalen**.
2. I **övre högra hörnet** av instrumentpanelen väljer du **Ta bort**.  När du har läst dialogrutan väljer du **Ja** för att **permanent ta bort** resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa build-versions- och versionsdefinitioner för att uppfylla behoven i ditt team. Du kan också använda det här CI/CD-mönstret som en mall för dina andra projekt.  Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET-app
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI-versionsdefinitionen
> * Granska VSTS CD Release Management-definitionen
> * Genomför ändringar av VSTS och distribuera automatiskt till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

Mer information om VSTS-pipeline finns i den här självstudien:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
