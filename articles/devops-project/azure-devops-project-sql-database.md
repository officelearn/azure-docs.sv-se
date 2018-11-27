---
title: 'Självstudie: Distribuera din ASP.NET-app och Azure SQL Database-kod med Azure DevOps-projekt'
description: Det är enkelt att komma igång med Azure med DevOps-projekt. Med DevOps-projekt kan du distribuera din ASP.NET-app och Azure SQL Database-kod i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 32f33e4ac66ad456b5ff8807d6a1b5ea5f541fed
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161409"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Självstudie: Distribuera din ASP.NET-app och Azure SQL Database-kod med Azure DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps-projekt gör även följande:
* Skapar automatiskt Azure-resurser, till exempel en Azure SQL-databas.
* Skapar och konfigurerar en versionspipeline i Azure Pipelines som innehåller en bygg-pipeline för CI.
* Konfigurerar en versionspipeline för CD. 
* Skapar en Azure Application Insights-resurs för övervakning.

I den här kursen ska du:

> [!div class="checklist"]
> * Använda Azure DevOps-projekt för att distribuera din ASP.NET-app och Azure SQL Database-kod
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Ansluta till Azure SQL-databasen 
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Skapa ett projekt i DevOps Projects för en ASP.NET-app och en Azure SQL-databas

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser som en Azure SQL-databas i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I sökrutan skriver du **DevOps Projects**, och välj sedan **Skapa**.

    ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett programramverk** väljer du **ASP.NET**.

1. Välj **Lägg till en databas** och sedan **Nästa**.  
    Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här. 
    
1. Välj **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din prenumeration för Azure-tjänster.  
    Alternativt kan du välja **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera användarnamnet i avsnittet **Inloggningsuppgifter för databasserver**. Lagra användarnamnet för kommande steg i den här självstudien. Om du utför det här valfria steget avslutar du konfigurationen av Azure-området innan du väljer **Klar**.
 
1. Välj **Done** (Klar).  
    Efter några minuter är processen klar, och DevOps Projects-instrumentpanelen öppnas i Azure-portalen. Du kan också navigera till instrumentpanelen direkt från **Alla resurser** i Azure-portalen. Till höger väljer du **Bläddra** för att visa programmet som körs.
    
## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en fullständig CI/CD-pipeline i Azure Repos. Du kan utforska och anpassa pipelinen. För att bekanta dig med Azure DevOps-bygg-pipelinen gör du följande:

1. Välj **Skapa pipelines** längst upp på DevOps Projects-instrumentpanelen.  
    En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj ellipsen (...).  
    En meny med flera alternativ visas, till exempel alternativ för att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I det här fönstret kan du granska de olika uppgifterna för bygg-pipelinen.  
    Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  
    I det här fönstret kan du se en spårningslogg över de senaste ändringarna för versionen. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
    DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  
    Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar också en CD-pipeline som tillhandahåller CD:n för den virtuella Azure-datorn. Om du vill veta mer om Azure DevOps CD-pipelinen kan du göra följande:

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  
    DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) bredvid din releasepipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

    DevOps Projects ställer in ett slumpmässigt lösenord för SQL och använder det för versionspipelinen.
    
1. Längst till vänster väljer du **Variabler**. 

  > [!NOTE]
  > Utför endast följande steg om du har ändrat lösenordet för SQL Server. Det finns en lösenordsvariabel.
  
1. Bredvid rutan **Värde** väljer du hänglåsikonen, anger det nya lösenordet och väljer därefter **Spara**.

1. Längst till vänster väljer du **Uppgifter** och sedan din miljö.  
    Uppgifter är de aktiviteter som distributionsprocessen kör och de grupperas ihop i faser. Den här versionspipelinen har enda fas som innehåller en *Azure App Service-distribution* och *Azure SQL Database-distribution*-uppgift.

1. Välj uppgiften *Kör Azure SQL* och undersök de olika egenskaperna som används för SQL-distribueringen.  
    Under **Distributionspaket** använder aktiviteten en *SQL DACPAC-fil*.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) bredvid en version och välj sedan **Öppna**.  
    Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
    Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  
    Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure 

 > [!NOTE]
 > Följande procedur testar CI/CD-pipeline med en enkel textändring. Du kan även göra en ändring i SQL Server-schemat i tabellen för att testa SQL-distributionsprocessen.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Kodändringarna initierar CI/CD-processen och distribuerar automatiskt dina ändringar till Azure.

1. I den vänstra rutan väljer **Kod** och går sedan till din lagringsplats.

1. Gå till katalogen *SampleWebApplication\Views\Home* och välj sedan ellipsen (...) bredvid filen *Index.cshtml* och välj sedan **Redigera**. 

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.  
    Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts kan du uppdatera ditt program för att verifiera dina ändringar.

## <a name="connect-to-the-azure-sql-database"></a>Ansluta till Azure SQL-databasen

Du behöver rätt behörigheter för att ansluta till Azure SQL-databasen.

1. Välj **SQL Database** från instrumentpanelen för DevOps Projects för att gå till hanteringssidan för SQL-databasen.
   
1. Välj **Konfigurera serverbrandvägg** och välj **Lägg till klientens IP-adress**. 

1. Välj **Spara**.  
    Din klient-IP-adress har nu åtkomst till SQL Server Azure-resursen.

1. Gå tillbaka till fönstret **SQL Database**. 

1. På höger sida väljer du servernamn för att gå till konfigurationssidan för **SQL Server**.

1. Välj **Återställ lösenord**, ange ett lösenord för inloggning för SQL Server och välj sedan **Spara**.  
    Tänk på att behålla det här lösenordet för att använda det senare i den här självstudien.

    Du kan nu även använda klientverktyg som SQL Server Management Studio eller Visual Studio för att ansluta till SQL Server och Azure SQL Database. Använd egenskapen **servernamn** för att ansluta.

    Om du inte ändrade databasanvändarnamnet när du först konfigurerade projektet i DevOps Projects är ditt användarnamn den lokala delen av din e-postadress. Om din e-postadress till exempel är *johndoe@microsoft.com* är ditt användarnamn *johndoe*.

 > [!NOTE]
 > Om du ändrar ditt lösenord för SQL-inloggningen måste du ändra lösenordet i variabeln för versionspipelinen enligt beskrivningen i avsnittet Granska CD-pipelinen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort Azure SQL-databasen och relaterade resurser som du skapade i den här självstudien. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. Funktionen *Ta bort* förstör alla data som skapats av Azure DevOps-projektet i både Azure och Azure DevOps, och du kan inte återskapa dem. Använd inte den här proceduren förrän du har läst anvisningarna noga.

1. Gå till DevOps Projects-instrumentpanelen i Azure-portalen.
2. Välj **Ta bort** i det övre högra hörnet. 
3. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda Azure DevOps-projekt för att distribuera din ASP.NET-app och Azure SQL Database-kod
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Ansluta till Azure SQL-databasen 
> * Rensa resurser

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videoklipp

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
