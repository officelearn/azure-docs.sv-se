---
title: Distribuera Azure Stream Analytics jobb med CI/CD och Azure DevOps
description: Den här artikeln beskriver hur du distribuerar ett Stream Analytics-jobb med CI/CD med hjälp av Azure DevOps Services.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 2e87432ad4437f41e70d988e7e2b3cd82aa3bd82
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123395"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Självstudie: Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av Azure Pipelines
Den här artikeln beskriver hur du konfigurerar kontinuerlig integrering och distribution för ett Azure Stream Analytics-jobb med hjälp av Azure Pipelines. 

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en bygg-pipeline i Azure Pipelines
> * Skapa en versionspipeline i Azure Pipelines
> * distribuera och uppgradera ett program automatiskt.

> [!NOTE]
> CI/CD-NuGet är föråldrad. Information om hur du migrerar till den senaste NPM finns i [Översikt över kontinuerlig integrering och distribution](cicd-overview.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har utfört följande steg:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) och arbetsbelastningen **Azure development** (Azure-utveckling) eller **Data Storage and Processing** (Datalagring och bearbetning).
* Skapa ett [Stream Analytics-projekt i Visual Studio](./stream-analytics-quick-create-vs.md).
* Skapa en [Azure DevOps](https://visualstudio.microsoft.com/team-services/) -organisation.

## <a name="configure-nuget-package-dependency"></a>Konfigurera NuGet-paketberoende
För att kunna utföra automatiskt skapande och automatisk distribution på en valfri virtuell dator måste du använda NuGet-paketet `Microsoft.Azure.StreamAnalytics.CICD`. Den ger verktygen för MSBuild, lokal körning och distribution som stöder processerna för kontinuerlig integrering och distribution av Stream Analytics Visual Studio-projekt. Mer information finns i [Stream Analytics CI/CD-verktyg](stream-analytics-tools-for-visual-studio-cicd.md).

Lägg till **packages.config** till projektkatalogen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Dela Visual Studio-lösningen till en ny Azure Repos Git-lagringsplats

Dela programkällfilerna till ett projekt i Azure DevOps så att du kan generera byggen.  

1. Skapa en ny lokal Git-lagringsplats för projektet genom att välja alternativet för att **lägga till källkontroll** och sedan **Git** i statusfältet i det nedre högra hörnet av Visual Studio. 

2. I vyn för **synkronisering** i **Team Explorer** väljer du knappen för att **publicera Git-lagringsplatsen** under alternativet för att **push-överföra till Azure DevOps Services** .

   ![Knappen Push to Azure DevOps Services Publish Git Repo (Publicera Git-lagringsplats för Azure DevOps-tjänster med push-överföring)](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Verifiera din e-postadress och välj din organisation i listrutan **Azure DevOps Services-domän** . Skriv in lagringsplatsens namn och välj **Publicera lagringsplats** .

   ![Knappen Push Git repo Publish Repository (Publicera Git-lagringsplats med push-överföring)](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    När du publicerar lagringsplatsen skapas ett nytt projekt i organisationen med samma namn som den lokala lagringsplatsen. Om du vill skapa lagrings platsen i ett befintligt projekt klickar du på **Avancerat** bredvid **databas namn** och väljer ett projekt. Du kan visa koden i webbläsaren genom att välja alternativet för att **visa på webben** .
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Konfigurera kontinuerlig leverans med Azure DevOps
En Azure Pipelines-bygg-pipeline beskriver ett arbetsflöde som består av byggesåtgärder som utförs i tur och ordning. Läs mer om [Azure Pipelines bygg-pipelines](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav&view=vsts).

En versionspipeline för Azure Pipelines beskriver ett arbetsflöde som distribuerar ett programpaket till ett kluster. När de används tillsammans kör bygg-pipelinen och versionspipelinen hela arbetsflödet med början på källfiler och slutar med ett program som körs i klustret. Läs mer om [Azure Pipelines versionspipelines](/azure/devops/pipelines/release/define-multistage-release-process?preserve-view=true&view=vsts).

### <a name="create-a-build-pipeline"></a>Skapa en bygg-pipeline
Öppna en webbläsare och gå till det projekt som du just skapade i [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Under fliken **Build & Release** (Bygge och version) väljer du **Builds** (Byggen) och sedan **+ New** (+ Nytt).  Välj **Azure DevOps Services Git** och **Fortsätt** .
    
    ![Välj källa för DevOps Git i Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. I **Välj en mall** klickar du på **Tom process** för att börja med en tom pipeline.
    
    ![Välj en tom process från mallalternativen i DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. Under **Utlösare** aktiverar du kontinuerlig integrering genom att markera utlösarstatusen **Aktivera kontinuerlig integrering** .  Välj alternativet för att **spara och köa** för att manuellt starta en version. 
    
    ![Aktivera utlösarstatus för kontinuerlig integrering](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Byggen utlöses också vid push-överföring och incheckning. Om du vill kontrol lera Bygg förloppet växlar du till fliken **versioner** .  När du har kontrollerat att versionen körs måste du definiera en versions pipeline som distribuerar ditt program till ett kluster. Högerklicka på ellipsen intill bygg-pipelinen och välj **Redigera** .

5.  I **Uppgifter** anger du "Hosted" som **Agent queue** (Agentkö).
    
    ![Välj agentkö på menyn Uppgifter](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. I **Fas 1** klickar du på **+** och lägger till en **NuGet** -uppgift.
    
    ![Lägga till en NuGet-uppgift i agentkö](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Expandera **Avancerat** och lägg till `$(Build.SourcesDirectory)\packages` till **målkatalogen** . Behåll återstående NuGet-standardkonfigurationsvärden.

   ![Konfigurera aktiviteten för återställning av NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. I **Fas 1** klickar du på **+** och lägger till en **MSBuild** -uppgift.

   ![Lägga till en MSBuild-uppgift i agentkö](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Ändra **MSBuild-argumenten** till följande:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Konfigurera MSBuild-uppgift i DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. I **Fas 1** , klickar du på **+** och lägger till en **Azure Resource Group Deployment** -uppgift. 
    
    ![Lägga till en Azure Resource Group Deployment-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Expandera **Azure Details** (Azure-information) och fyll i konfigurationen med följande:
    
    |**Inställning**  |**Föreslaget värde**  |
    |---------|---------|
    |Prenumeration  |  Välj din prenumeration.   |
    |Åtgärd  |  Skapa eller uppdatera resursgrupp   |
    |Resursgrupp  |  Ange ett resursgruppsnamn.   |
    |Mall  | [Lösningens sökväg]\bin\Debug\Deploy\\[Projektnamnet].JobTemplate.json   |
    |Mallparametrar  | [Lösningens sökväg]\bin\Debug\Deploy\\[Projektnamnet].JobTemplate.parameters.json   |
    |Åsidosätt mallparametrar  | Skriv in mallparametrarna som ska åsidosättas i textrutan. Exempel `–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre)` . Den här egenskapen är valfri, men bygget kommer att resultera i fel om nyckelparametrarna inte åsidosätts.    |
    
    ![Ange egenskaper för distribution av Azure-resursgrupper](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Klicka på **Save & Queue** (Spara och köa) för att testa bygg-pipelinen.
    
    ![Spara och köa version i DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Misslyckad byggeprocess
Det kan hända att du får fel för nulldistributionsparametrar om du inte åsidosatte mallparametrarna i **Azure Resource Group Deployment** -uppgiften för bygg-pipelinen. Gå tillbaka till bygg-pipelinen och åsidosätt nullparametrarna för att åtgärda problemet.

   ![DevOps Stream Analytics-genereringsprocessen misslyckades](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Checka in och push-överför ändringar för att utlösa en frisläppning
Kontrollera att pipelinen för den kontinuerliga integreringen fungerar genom att kontrollera kodändringar i Azure DevOps.    

När du skriver koden spåras dina ändringar automatiskt av Visual Studio. Genomför ändringar av den lokala Git-lagringsplatsen genom att välja ikonen väntande ändringar från statusfältet längst ned till höger.

1. I vyn **Ändringar** i Team Explorer lägger du till ett meddelande som beskriver uppdateringen. Spara sedan ändringarna.

    ![Genomföra lagringsplatsändringar från Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Välj statusfältikonen för de opublicerade ändringarna eller vyn Synkronisera i Team Explorer. Välj **Push** (Push-överföring) för att uppdatera koden i Azure DevOps.

    ![Skicka ändringar från Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

När du skickar ändringar till Azure DevOps Services via push-överföring utlöses ett bygge automatiskt.  När bygg-pipelinen har slutförts skapas en version automatiskt och börjar uppdatera jobbet i klustret.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i självstudien med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.  
2. På sidan med resursgrupper klickar du på **Ta bort** , skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort** .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Azure Stream Analytics verktyg för Visual Studio för att konfigurera en kontinuerlig integrering och distributions process fortsätter du till artikeln Konfigurera CI/CD-pipeline:

> [!div class="nextstepaction"]
> [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)