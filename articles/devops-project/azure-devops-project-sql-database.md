---
title: Distribuera din ASP.NET-app och Azure SQL Database med Azure DevOps-projekt | VSTS-självstudier
description: DevOps-projekt gör det enkelt att komma igång med Azure. Azure DevOps-projekt gör det enkelt att distribuera din ASP.NET med Azure SQL Database i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: ba15a57f92ad58a46732444890d9e670fcc75cf3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967420"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Självstudie: Distribuera din ASP.NET-app och Azure SQL Database med Azure DevOps-projekt

Azure DevOps-projektet ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.  DevOps-projektet skapar automatiskt Azure-resurser som en Azure SQL Database, skapar och konfigurerar en versionspipeline i VSTS som innehåller en versionsdefinition för CI, ställer in en versionsdefinition för CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET-app och Azure SQL Database
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI-versionsdefinitionen
> * Granska VSTS CD Release Management-definitionen
> * Genomför ändringar av VSTS och distribuera automatiskt till Azure
> * Ansluta till SQL Server-databasen 
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Skapa ett Azure DevOps-projekt för en ASP.NET-app och Azure SQL Database

Azure DevOps-projektet skapar en CI/CD-pipeline i VSTS.  Du kan skapa ett **nytt VSTS**-konto eller använda ett **befintligt konto**.  Azure DevOps-projektet skapar även **Azure-resurser** som en Azure SQL Database i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **skapa en resurs** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **.NET** och välj sedan **Nästa**.

1. För **Välj ett programramverk** väljer du **ASP.NET**.

1. Välj **Lägg till en databas** och sedan **Nästa**.  

1. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här.  Välj **Nästa**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurera VSTS och en Azure-prenumeration

1. Skapa ett **nytt** VSTS-konto eller välj ett **befintligt** konto.  Välj ett **namn** för ditt VSTS-projekt.  

1. Välj din **Azure-prenumeration**.

1. Alternativt kan du välja länken **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera **användarnamnet** i avsnittet **Inloggningsuppgifter för databasserver**.  **Lagra** **användarnamnet** för kommande steg i den här självstudien.
 
1. Om du utförde steget ovan avslutar du Azure-konfigurationsområdet och väljer **Klar**.  Annars väljer du bara **Klar**.

1. Det tar flera minuter för processen att slutföras.  När det är klart läses Azure DevOps **projektinstrumentpanel** in i Azure-portalen.  Du kan också navigera till **instrumentpanelen för Azure DevOps-projektet** direkt från **Alla resurser** i **Azure-portalen**.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa programmet som körs.
    
## <a name="examine-the-vsts-ci-build-definition"></a>Granska VSTS CI-versionsdefinitionen

Azure DevOps-projektet konfigurerar automatiskt en fullständig VSTS-CI/CD-pipeline i VSTS-kontot.  Du kan utforska och anpassa pipelinen.  Följ stegen nedan för att bekanta dig med VSTS-versionsdefinitionen.

1. Navigera till **instrumentpanelen för Azure DevOps-projektet**.

1. Välj **Skapa pipelines** **längst upp** på **instrumentpanelen för Azure DevOps-projektet**.  Den här länken öppnar en flik i webbläsaren och öppnar VSTS versionsdefinition för det nya projektet.

1. Flytta markören till höger om versionsdefinition bredvid fältet **Status**. Välj **ellipsen** som visas.  Den här åtgärden öppnar en meny där du kan utföra flera aktiviteter, till exempel att **lägga till en ny version i en kö**, **pausa en version** och **redigera versionsdefinitionen**.

1. Välj **Redigera**.

1. **Granska de olika uppgifterna** för versionsdefinition från den här vyn.  Versionen kör olika uppgifter som att hämta källor från VSTS Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Längst upp i versionsdefinitionen väljer du **build definition name** (namn på versionsdefinitionen).

1. Ändra **namnet** på din versionsdefinition till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **spara**.

1. Under ditt versionsdefinitionsnamn väljer du **historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  VSTS spårar alla ändringar som görs på versionsdefinition vilket gör att du kan jämföra versioner.

1. Välj **utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **kvarhållning**.  Du kan ange principer för att behålla eller ta bort ett visst antal versioner baserat på ditt scenario.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Granska VSTS CD Release Management-definitionen

Azure DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att distribuera från ditt VSTS-konto på Azure-prenumerationen automatiskt.  De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera VSTS till din Azure-prenumeration.  Automationen skapar också en VSTS-versionsdefinition och versionen ger CD:n till Azure.  Följ stegen nedan för att få veta mer om VSTS-versionsdefinitionen.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en VSTS-versionsdefinition för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionsdefinition och sedan väljer du **redigera**.

1. Versionsdefinitionen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den versionsdefinition du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **ikonen** **Utlösare av kontinuerlig distribution** (som ser ut som en blixt).  Den här versionen har en aktiverad CD-utlösare.  Utlösaren startar en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Azure DevOps-projektet konfigurerade ett slumpmässigt lösenord för SQL och använde det här lösenordet för versionsdefinitionen.  På vänster sida i webbläsaren väljer du **Variabler**. 

1. **Utför endast det här steget om du har ändrat lösenordet för SQL Server.**  Det finns en **lösenordsvariabel**.  Till höger om textrutan **Värde** väljer du ikonen med ett **hänglås**.  **Ange** nytt lösenord och välj sedan **spara**.

1. På vänster sida i webbläsaren väljer du **Uppgifter** och sedan väljer du **miljö**.  

1. Uppgifter är de aktiviteter som distributionsprocessen utför och de grupperas ihop i **Faser**.  Det finns en enda fas för den här versionsdefinitionen.  Fasen innehåller en **Azure App Service-distributionen**-uppgift och **Azure SQL Database-distribution**-uppgift.

1. Välj uppgiften **Kör Azure SQL** och undersök de olika egenskaperna som används för SQL-distribueringen.  Under **Distributionspaket** observerar du att aktiviteten använder en **SQL DACPAC-fil**.

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en **versionssammanfattning**, **tillhörande arbetsobjekt** och **tester**.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. Du kan jämföra versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Genomför ändringar av VSTS och distribuera automatiskt till Azure 

 > [!NOTE]
 > Stegen nedan testar CI/CD-pipeline med en enkel textändring.  Du kan även göra en ändring i SQL Server-schemat i tabellen för att testa SQL-distributionsprocessen.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring i VSTS-git-lagringsplatsen startar en version i VSTS och en VSTS Release Management-definition kör en distribution till Azure.  Följ stegen nedan eller använd andra metoder för att göra ändringar till databasen.  Kodändringarna startar CI/CD-processen och distribuerar automatiskt dina ändringar till Azure.

1. Välj **kod** från VSTS-menyn och navigera till din lagringsplats.

1. Navigera till katalogen **SampleWebApplication\Views\Home** och välj sedan **ellipsen** bredvid filen **Index.cshtml** och välj sedan **redigera** .

1. Gör en ändring i filen, till exempel i texten i en av **div-taggarna**.  Välj **Genomför** i det övre högra hörnet.  Välj **Genomför** igen för att push-överföra ändringen. 

1. Efter en stund **startar en version i VSTS** och därefter körs en version för att distribuera ändringarna.  Du kan övervaka **build status** (versionstillståndet) med instrumentpanelen för DevOps-projektet eller i webbläsaren med ditt VSTS-konto.

1. När lanseringen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="connect-to-the-azure-sql-server-database"></a>Ansluta till SQL Server-databasen

Du behöver rätt behörigheter för att ansluta till Azure SQL Database.

1. Välj **SQL Database** från instrumentpanelen för Azure DevOps-projektet för att navigera till hanteringssidan för SQL DB.
   
1. Välj **Konfigurera serverbrandvägg** och välj **+ Lägg till klientens IP-adress**.  

1. Välj **Spara**.  Din klient-IP-adress har nu åtkomst till **SQL Server Azure-resursen**.

1. Gå tillbaka till bladet **SQL Database**. 

1. På höger sida av skärmen väljer du **Servernamn** för att gå till konfigurationssidan för **SQL Server**.

1. Välj **Återställ lösenord**, ange ett lösenord för **SQL Server admin login** (Inloggning för SQL Server-administratör) och välj sedan **Spara**.  **Behåll** lösenordet för kommande steg i den här självstudien.

1. Du kan nu även använda klientverktyg som **SQL Server Management Studio** eller **Visual Studio** för att ansluta till Azure SQL Server och Database.  Använd egenskapen **servernamn** för att ansluta.

   Om du inte ändrade DB-användarnamnet när du först konfigurerade DevOps-projektet är ditt användarnamn den lokala delen av din e-postadress.  Om din e-postadress till exempel är johndoe@microsoft.com är ditt användarnamn är johndoe.

 > [!NOTE]
 > Om du ändrar ditt lösenord för SQL-inloggningen kan du behöva ändra lösenordet i variabeln för VSTS-versionsdefinitionen enligt beskrivningen i avsnittet **Granska VSTS CD Release Management-definitionen**

## <a name="clean-up-resources"></a>Rensa resurser

 > [!NOTE]
 > Stegen nedan tar permanent bort resurser.  Använd endast den här funktionen efter att ha läst anvisningarna noga.

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter.  När du inte längre behöver det kan du ta bort Azure SQL Database och relaterade resurser som skapats i den här självstudien med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projekt.  **Var försiktig** eftersom funktionen Ta bort förstör alla data som skapats av Azure DevOps-projektet i både Azure och VSTS. Du kommer inte att kunna återskapa dem när de har tagits bort.

1. Gå till **Azure DevOps-projekt** från **Azure-portalen**.
2. I **övre högra hörnet** av instrumentpanelen väljer du **Ta bort**.  När du har läst dialogrutan väljer du **Ja** för att **permanent ta bort** resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa build-versions- och versionsdefinitioner för att uppfylla behoven i ditt team. Du kan också använda det här CI/CD-mönstret som en mall för dina andra projekt.  Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET-app och Azure SQL Database
> * Konfigurera VSTS och en Azure-prenumeration 
> * Granska VSTS CI-versionsdefinitionen
> * Granska VSTS CD Release Management-definitionen
> * Genomför ändringar av VSTS och distribuera automatiskt till Azure
> * Ansluta till SQL Server-databasen 
> * Rensa resurser

Mer information om VSTS-pipeline finns i den här självstudien:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videoklipp

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]