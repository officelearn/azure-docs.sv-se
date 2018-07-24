---
title: Självstudien Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av VSTS
description: Den här artikeln beskriver hur du distribuerar ett Stream Analytics-jobb med CI/CD med hjälp av VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: 303c1cfaf2b91712f706c5b78e027bb02739c770
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074602"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Självstudie: Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av VSTS
Den här artikeln beskriver hur du konfigurerar kontinuerlig integrering och distribution för ett Azure Stream Analytics-jobb med hjälp av Visual Studio Team Services. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en byggesdefinition i Team Services
> * skapa en versionsdefinition i Team Services
> * distribuera och uppgradera ett program automatiskt.

## <a name="prerequisites"></a>Nödvändiga komponenter
Se till att du har följande innan du börjar:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) och arbetsbelastningen **Azure development** (Azure-utveckling) eller **Data Storage and Processing** (Datalagring och bearbetning).
* Skapa ett [Stream Analytics-projekt i Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs).
* Skapa ett [Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)-konto.

## <a name="configure-nuget-package-dependency"></a>Konfigurera NuGet-paketberoende
För att kunna utföra automatiskt skapande och automatisk distribution på en valfri virtuell dator måste du använda NuGet-paketet `Microsoft.Azure.StreamAnalytics.CICD`. Den ger verktygen för MSBuild, lokal körning och distribution som stöder processerna för kontinuerlig integrering och distribution av Stream Analytics Visual Studio-projekt. Mer information finns i [Stream Analytics CI/CD-verktyg](stream-analytics-tools-for-visual-studio-cicd.md).

Lägg till **packages.config** till projektkatalogen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Dela Visual Studio-lösningen till en ny Team Services Git-lagringsplats
Dela programkällfilerna till ett teamprojekt i Team Services så att du kan generera byggen.  

1. Skapa en ny lokal Git-lagringsplats för projektet genom att välja alternativet för att **lägga till källkontroll** och sedan **Git** i statusfältet i det nedre högra hörnet av Visual Studio. 

2. I vyn för **synkronisering** i **Team Explorer** väljer du knappen för att **publicera Git-lagringsplatsen** under alternativet för att **push-överföra till Visual Studio Team Services**.

   ![Push-överföring med Git-lagringsplats](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Verifiera din e-postadress och välj ditt konto i listrutan **Team Services-domän**. Skriv in lagringsplatsens namn och välj **Publicera lagringsplats**.

   ![Push-överföring med Git-lagringsplats](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    När du publicerar lagringsplatsen skapas ett nytt teamprojekt i kontot med samma namn som den lokala lagringsplatsen. Om du vill skapa lagringsplatsen i ett befintlig teamprojekt klickar du på **Avancerat** bredvid **namnet på databasen** och väljer ett teamprojekt. Du kan visa koden i webbläsaren genom att välja alternativet för att **visa på webben**.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurera kontinuerlig leverans med VSTS
En Team Services-byggesdefinition beskriver ett arbetsflöde som består av byggesåtgärder som utförs i tur och ordning. Ta reda på mer om [byggesdefinitioner för Team Services](https://www.visualstudio.com/docs/build/define/create). 

En versionsdefinition för Team Services beskriver ett arbetsflöde som distribuerar ett programpaket till ett kluster. När de används tillsammans kör byggesdefinitionen och versionsdefinitionen hela arbetsflödet med början på källfiler och slutar med ett program som körs i klustret. Lär dig mer om [versionsdefinitioner för Team Services](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>Skapa en byggesdefinition
Öppna en webbläsare och gå till det teamprojekt som du just skapade i [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Under fliken **Build & Release** (Bygge och version) väljer du **Builds** (Byggen) och sedan **+ New** (+ Nytt).  Välj **VSTS Git** och **Fortsätt**.
    
    ![Välj källa](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. I **Välj en mall** klickar du på **Tom process** för att börja med en tom definition.
    
    ![Välj byggesmall](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Under **Utlösare** aktiverar du kontinuerlig integrering genom att markera utlösarstatusen **Aktivera kontinuerlig integrering**.  Välj alternativet för att **spara och köa** för att manuellt starta en version. 
    
    ![Utlösarstatus](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Byggen utlöses också vid push-överföring och incheckning. Om du vill kontrollera förloppet för bygget växlar du till fliken **Builds** (Byggen).  När du har kontrollerat att bygget körs rätt måste du definiera en versionsdefinition som distribuerar programmet till ett kluster. Högerklicka på ellipsen intill byggesdefinitionen och välj **redigera**.

5.  I **Uppgifter** anger du "Hosted" som **Agent queue** (Agentkö).
    
    ![Välja agentkö](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. I **Fas 1** klickar du på **+** och lägger till en **NuGet**-uppgift.
    
    ![Lägga till en NuGet-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Expandera **Avancerat** och lägg till `$(Build.SourcesDirectory)\packages` till **målkatalogen**. Behåll återstående NuGet-standardkonfigurationsvärden.

   ![Konfigurera NuGet-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. I **Fas 1** klickar du på **+** och lägger till en **MSBuild**-uppgift.

   ![Lägga till MSBuild-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Ändra **MSBuild-argumenten** till följande:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Konfigurera MSBuild-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. I **Fas 1**, klickar du på **+** och lägger till en **Azure Resource Group Deployment**-uppgift. 
    
    ![Lägga till en Azure Resource Group Deployment-uppgift](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Expandera **Azure Details** (Azure-information) och fyll i konfigurationen med följande:
    
    |**Inställning**  |**Föreslaget värde**  |
    |---------|---------|
    |Prenumeration  |  Välj din prenumeration.   |
    |Åtgärd  |  Skapa eller uppdatera resursgrupp   |
    |Resursgrupp  |  Ange ett resursgruppsnamn.   |
    |Mall  | [Lösningens sökväg]\bin\Debug\Deploy\\[Projektnamnet].JobTemplate.json   |
    |Mallparametrar  | [Lösningens sökväg]\bin\Debug\Deploy\\[Projektnamnet].JobTemplate.parameters.json   |
    |Åsidosätt mallparametrar  | Skriv in mallparametrarna som ska åsidosättas i textrutan. Example, –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Den här egenskapen är valfri, men bygget kommer att resultera i fel om nyckelparametrarna inte åsidosätts.    |
    
    ![Ange egenskaper](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Klicka på **Save & Queue** (Spara och köa) för att testa byggesdefinition.
    
    ![Ange åsidosättningsparametrar](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Misslyckad byggeprocess
Det kan hända att du får fel för nulldistributionsparametrar om du inte åsidosatte mallparametrarna i **Azure Resource Group Deployment**-uppgiften för byggesdefinitionen. Gå tillbaka till byggesdefinitionen och åsidosätt nullparametrarna för att åtgärda problemet.

   ![Byggeprocessen misslyckades](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Checka in och push-överför ändringar för att utlösa en frisläppning
Kontrollera att pipelinen för den kontinuerliga integreringen fungerar genom att kontrollera kodändringar i Team Services.    

När du skriver koden spåras dina ändringar automatiskt av Visual Studio. Genomför ändringar av den lokala Git-lagringsplatsen genom att välja ikonen väntande ändringar från statusfältet längst ned till höger.

1. I vyn **Ändringar** i Team Explorer lägger du till ett meddelande som beskriver uppdateringen. Spara sedan ändringarna.

    ![Checka in och push-överför ändringar](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Välj statusfältikonen för de opublicerade ändringarna eller vyn Synkronisera i Team Explorer. Välj **Push** (Push-överföring) för att uppdatera koden i Team Services/TFS.

    ![Checka in och push-överför ändringar](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

När du skickar ändringar till Team Services via push-överföring utlöser ett bygge automatiskt.  När byggesdefinitionen har slutförts skapas en version automatiskt och börjar uppdatera jobbet i klustret.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömningsenheter som förbrukas av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i självstudien med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.  
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Stream Analytics-verktyg för Visual Studio för att konfigurera en kontinuerlig integrerings- och distributionsprocess finns i artikeln om konfiguration av CI/CD-pipeline:

> [!div class="nextstepaction"]
> [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)