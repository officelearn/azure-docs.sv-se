---
title: Distribuera din app till Azure och Azure Stack | Microsoft Docs
description: "Lär dig mer om att distribuera appar till Azure och Azure-stacken med en hybrid CI/CD-pipeline."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 6292a846a2c3d7e112558ef0d2b62b3a3fdd3c51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Distribuera appar till Azure och Azure Stack
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

En hybrid [kontinuerlig integration](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[kontinuerlig leverans](https://www.visualstudio.com/learn/what-is-continuous-delivery/)pipeline (CI/CD) gör det möjligt att bygga, testa och distribuera din app till flera moln.  I den här självstudiekursen skapar du en exempel-miljön om du vill veta hur en hybrid CI/CD-pipeline kan hjälpa dig:
 
> [!div class="checklist"]
> * Initiera en ny version utifrån kod incheckningar till lagringsplatsen för Visual Studio Team Services VSTS ().
> * Distribuera automatiskt nyligen inbyggd koden till Azure för användargodkännande.
> * När koden har gått testning, automatiskt distribuera till Azure-stacken. 


## <a name="prerequisites"></a>Förutsättningar
Några komponenter krävs för att skapa en hybrid CI/CD-pipeline och kan ta lite tid att förbereda.  Om du redan har vissa av dessa komponenter, kontrollera att de uppfyller krav innan du börjar.

Det här avsnittet förutsätter också att du har viss erfarenhet av Azure och Azure-stacken. Om du vill veta mer innan du fortsätter bör du börja med dessa avsnitt:

- [Introduktion till Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Azure-stacken viktiga begrepp](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
 - Skapa en [Webbapp](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md), och konfigurerar den för [FTP-publicering](../../app-service/app-service-deploy-ftp.md).  Anteckna den nya Web App-URL, eftersom den används senare.


### <a name="azure-stack"></a>Azure Stack
 - [Distribuera Azure Stack](../azure-stack-run-powershell-script.md).  Installationen tar vanligtvis några timmar att slutföra, så planera på lämpligt sätt.
 - Distribuera [Apptjänst](../azure-stack-app-service-deploy.md) PaaS-tjänster till Azure-stacken.
 - Skapa en Webbapp och konfigurerar den för [FTP-publicering](../azure-stack-app-service-enable-ftp.md).  Anteckna den nya Web App-URL, eftersom den används senare.  

### <a name="developer-tools"></a>Utvecklarverktyg
 - Skapa en [VSTS arbetsytan](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  Registreringsprocessen skapar ett projekt med namnet ”MyFirstProject”.  
 - [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) och [logga in på VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - Ansluta till projektet och [klona lokalt](https://www.visualstudio.com/docs/git/gitquickstart).
 - Skapa en [agent poolen](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) i VSTS.
 - Installera Visual Studio och distribuera en [VSTS skapa agenten](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) till en virtuell dator på Azure-stacken. 
 

## <a name="create-app--push-to-vsts"></a>& Skapa app push till VSTS

### <a name="create-application"></a>Skapa program
I det här avsnittet kan du skapa ett enkelt ASP.NET-program och push-installera den VSTS.  De här stegen representerar normala developer arbetsflödet och kan anpassas för utvecklingsverktyg och språk. 

1.  Öppna Visual Studio.
2.  Från området Team Explorer och **lösningar...**  området klickar du på **ny**.
3.  Välj **Visual C#** > **Web** > **ASP.NET-webbprogram (.NET Framework)**.
4.  Ange ett namn för programmet och klickar på **OK**.
5.  På nästa skärm Behåll standardvärdena (Web forms) och klicka på **OK**.

### <a name="commit-and-push-changes-to-vsts"></a>Spara och skicka ändringar till VSTS
1.  Använder teamet Explorer i Visual Studio, Välj i listrutan och klicka på **ändringar**.
2.  Ange ett meddelande för genomförande och välj **genomför alla**. Du kan uppmanas att spara filen i lösningen, klicka på Ja om du vill spara alla.
3.  När allokerat, erbjuder Visual Studio att synkronisera ändringar i projektet. Välj **Synkronisera**.

    ![bild som visar skärmen för genomförande när genomförande har slutförts](./media/azure-stack-solution-pipeline/image1.png)

4.  På fliken synkronisering under *utgående*, du kan se din nya genomförande.  Välj **Push** att synkronisera VSTS ändringen.

    ![bild som visar sync steg](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Granska koden i VSTS
När du har allokerat en ändring och pushas till VSTS, kontrollera din kod från VSTS-portalen.  Välj **kod**, och sedan **filer** från listrutan.  Du kan se den lösning som du skapade.

## <a name="create-build-definition"></a>Skapa build-definition
Skapar definierar hur programmet har skapats och packade för distribution på varje genomförande av kodändringar. I vårt exempel använder vi den inkluderade mallen för att konfigurera build-process för en ASP.NET-app, även om den här konfigurationen kan anpassas beroende på ditt program.

1.  Logga in på arbetsytans VSTS från en webbläsare.
2.  Popup-meddelandet, Välj **Skapa & släpper** och sedan **bygger**.
3.  Klicka på **+ ny definition**.
4.  Välj i listan över mallar **ASP.NET (förhandsgranskning)** och välj **tillämpa**.
5.  Ändra den *MSBuild-argument* i *skapa lösning* steg att:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Välj den **alternativ** och välj agent kön för build-agenten som du har distribuerat till en virtuell dator på Azure-stacken. 
7.  Välj den **utlösare** och aktivera **kontinuerlig Integration**.
7.  Klicka på **Spara & kö** och välj sedan **spara** i listrutan. 

## <a name="create-release-definition"></a>Skapa en definition för versionen
Processen för versionen definierar hur versioner från föregående steg har distribuerats till en miljö.  I den här självstudiekursen publicera vi vår ASP.NET-app med FTP till en Webbapp i Azure. Använd följande steg för att konfigurera en Versionspost till Azure:

1.  Välj VSTS-banderoll **Skapa & släpper** och sedan **versioner**.
2.  Klicka på gröna **+ ny definition**.
3.  Välj **tom** och på **nästa**.
4.  Markera kryssrutan för *kontinuerlig distribution*, och klicka sedan på **skapa**.

Nu när du har skapat en tom versionen definition och knutna till bygga, vi lägga till stegen för Azure-miljön:

1.  Klicka på gröna  **+**  att lägga till aktiviteter.
2.  Välj **alla**, och i listan Lägg till **FTP överför** och välj **Stäng**.
3.  Välj den **FTP överför** uppgift du just har lagt till och konfigurera följande parametrar:
    
    | Parameter | Värde |
    | ----- | ----- |
    |Autentiseringsmetod| Ange autentiseringsuppgifter|
    |Server-URL | FTP-URL för Web App hämtas från Azure-portalen |
    |Användarnamn | Användarnamnet som du konfigurerade när du skapar FTP-autentiseringsuppgifter för webbprogram |
    |Lösenord | Lösenordet du skapade när FTP-autentiseringsuppgifter för webbprogram|
    |Källkatalog | $(System.DefaultWorkingDirectory)\**\ |
    |Fjärrkatalog | /site/wwwroot/ |
    |Bevara sökvägar | Aktiverat (markerad)|

4.  Klicka på **spara**

Slutligen kan du konfigurera definitionen versionen om du vill använda agent poolen innehåller agenten distribueras med följande steg:
1.  Markera versionen definitionen och på **redigera**.
2.  Välj **körs på agent** från den mellersta kolumnen.  Välj agent kön som innehåller build-agenten som körs på Azure-stacken i den högra kolumnen.  
    ![bild som visar konfigurationen av versionen definitionen för att använda en särskild kö](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Distribuera din app till Azure
Det här steget använder din nyligen inbyggd CI/CD-pipeline för att distribuera ASP.NET-appen till en Webbapp i Azure. 

1.  Välj banderoll i VSTS **Skapa & släpper**, och välj sedan **bygger**.
2.  Klicka på **...**  på build-definition tidigare skapade och välj **kö ny version**.
3.  Acceptera standardinställningarna och klickar på **Ok**.  Bygga börjar och visar förloppet.
4.  När versionen har slutförts kan du spåra statusen genom att välja **Skapa & släpper** och välja **versioner**.
5.  När bygga är klar kan du gå till webbplatsen med den URL som anges när du skapar Webbappen.    


## <a name="add-azure-stack-to-pipeline"></a>Lägg till Azure-stacken pipeline
Nu när du har testat din CI/CD-pipeline genom att distribuera till Azure, är det dags att lägga till Azure-stacken i pipelinen.  I följande steg ska du skapa en ny miljö och Lägg till en FTP-överför aktivitet för att distribuera din app till Azure-stacken.  Du också lägga till en godkännare för versionen som fungerar som ett sätt att simulera ”signering” på en code-versionen till Azure-stacken.  

1.  I version-definition väljer **+ Lägg till miljön** och **skapa nya miljön**.
2.  Välj **tom**, klickar du på **nästa**.
3.  Välj **specifika användare** och ange ditt konto.  Välj **Skapa**.
4.  Byt namn på miljön genom att markera det befintliga namnet och skriva *Azure Stack*.
5.  Nu markeringen Azure Stack-miljön, välj sedan **lägga till aktiviteter**.
6.  Välj den **FTP överför** uppgift och välj **Lägg till**och välj **Stäng**.


### <a name="configure-ftp-task"></a>Konfigurera FTP-aktivitet
Nu när du har skapat en Versionspost, konfigurerar du de steg som krävs för publicering till webb-App på Azure-stacken.  Precis som du har konfigurerat överför FTP-aktivitet för Azure kan du konfigurera aktiviteten för Azure-Stack:

1.  Välj den **FTP överför** uppgift du just har lagt till och konfigurera följande parametrar:
    
    | Parameter | Värde |
    | -----     | ----- |
    |Autentiseringsmetod| Ange autentiseringsuppgifter|
    |Server-URL | FTP-URL för Web App hämtas från stacken Azure portal |
    |Användarnamn | Användarnamnet som du konfigurerade när du skapar FTP-autentiseringsuppgifter för webbprogram |
    |Lösenord | Lösenordet du skapade när FTP-autentiseringsuppgifter för webbprogram|
    |Källkatalog | $(System.DefaultWorkingDirectory)\**\ |
    |Fjärrkatalog | /site/wwwroot/|
    |Bevara sökvägar | Aktiverat (markerad)|

2.  Klicka på **spara**

Konfigurera slutligen definitionen versionen om du vill använda agent poolen innehåller agenten distribueras med följande steg:
1.  Markera versionen definitionen och på **redigera**
2.  Välj **körs på agent** från den mellersta kolumnen. Välj agent kön som innehåller build-agenten som körs på Azure-stacken i den högra kolumnen.  
    ![bild som visar konfigurationen av versionen definitionen för att använda en särskild kö](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Distribuera nya kod
Nu kan du testa hybrid CI/CD-pipelinen, med det sista steget publicering till Azure-stacken.  I det här avsnittet att ändra platsens sidfoten och starta distribution via pipeline.  När installationen är klar visas ändringarna distribueras till Azure för granskning och när du godkänner versionen de publiceras till Azure-stacken.

1. Öppna i Visual Studio den *site.master* filen och ändra den här raden:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    till denna:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Spara ändringarna och synkronisera VSTS.  
4.  Från arbetsytan VSTS statuskontroll build genom att välja **Skapa & släpper** > **skapa**
5.  Du ser en build pågår.  Dubbelklicka på status och du kan se förloppet build.  När du ser ”klar build” i konsolen, går du vidare till Kontrollera versionen från **Skapa & släpper** > **släpper**.  Dubbelklicka på versionen.
6.  Får du ett meddelande att granska kräver att en version. Kontrollera Webbappens URL och de nya ändringarna finns.  Godkänn version i VSTS.
    ![bild som visar konfigurationen av versionen definitionen för att använda en särskild kö](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Kontrollera publicering till Azure-stacken är klar genom att besöka webbplatsen med den URL som anges när du skapar Webbappen.
    ![bild som visar ASP. NEt app med sidfot ändras](./media/azure-stack-solution-pipeline/image5.png)


Du kan nu använda din nya hybrid CI/CD-pipeline som ett byggblock för andra hybrid cloud-mönster.

## <a name="next-steps"></a>Nästa steg
I kursen får du lärt dig hur du skapar en hybrid CI/CD-pipeline som:

> [!div class="checklist"]
> * Initierar en ny version utifrån kod genomför till lagringsplatsen för Visual Studio Team Services VSTS ().
> * Distribuerar automatiskt nyligen inbyggd koden till Azure för användargodkännande.
> * När koden har gått testning, distribuerar automatiskt till Azure-stacken. 

Nu när du har en hybrid CI/CD-pipeline kan fortsätta genom att lära dig hur du utvecklar program för Azure-stacken.

> [!div class="nextstepaction"]
> [Utveckla för Azure Stack](azure-stack-developer.md)


