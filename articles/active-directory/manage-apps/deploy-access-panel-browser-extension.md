---
title: Distribuera Azure Access Panel-tillägg för Internet Explorer med hjälp av ett grupprincipobjekt | Microsoft Docs
description: Hur du använder grupprinciper för att distribuera Internet Explorer-tillägget för Mina appar-portalen.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a21a21f40e731e8bc1d20e01d3671c372df65d84
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622048"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip
Den här självstudien visar hur du använder grupprinciper för att fjärrinstallera Access Panel-tillägg för Internet Explorer på användarnas datorer. Det här tillägget är obligatoriskt för Internet Explorer-användare som behöver logga in på appar som har konfigurerats med hjälp av [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md#password-based-sso).

Vi rekommenderar att administratörer automatisera distributionen av det här tillägget. I annat fall behöver användarna ladda ned och installera tillägget själva, som är lätt fel och måste ha administratörsbehörighet. Den här självstudien beskriver en metod för att automatisera distribution av programvara med hjälp av en Grupprincip. [Läs mer om en Grupprincip.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Åtkomstpanel-tillägget är också tillgängligt för [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) och [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), av vilka kräver administratörsbehörighet för att installera.

## <a name="prerequisites"></a>Förutsättningar
* Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit dina användares datorer till domänen.
* Du måste ha behörigheten ”Redigera inställningar” så här redigerar du den grupprincipobjekt (GPO). Som standard medlemmar i de följande säkerhetsgrupperna har denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Steg 1: Skapa distributionsplatsen.
Först måste du placera installer-paketet på en nätverksplats som kan användas av de datorer som du vill fjärrinstallera tillägget på. Det gör du genom att följa dessa steg:

1. Logga in på servern som administratör
2. I den **Serverhanteraren** gå till fönstret **filer och lagringstjänster**.
   
    ![Öppna filer och lagringstjänster](./media/deploy-access-panel-browser-extension/files-services.png)
3. Gå till den **resurser** fliken. Klicka sedan på **uppgifter** > **ny resurs...**
   
    ![Öppna filer och lagringstjänster](./media/deploy-access-panel-browser-extension/shares.png)
4. Slutför den **guiden Ny resurs** och definiera behörigheter för att säkerställa att den kan nås från användarnas datorer. [Läs mer om resurser.](https://technet.microsoft.com/library/cc753175.aspx)
5. Hämta följande Microsoft Windows Installer-paket (MSI-fil): [åtkomst panelen Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
6. Kopiera installer-paketet till en önskad plats på resursen.
   
    ![Kopiera MSI-filen till resursen.](./media/deploy-access-panel-browser-extension/copy-package.png)
7. Kontrollera att dina klientdatorer ska kunna komma åt installationspaketet från filresursen. 

## <a name="step-2-create-the-group-policy-object"></a>Steg 2: Skapa grupprincipobjektet
1. Logga in på den server som är värd för din Active Directory Domain Services (AD DS)-installation.
2. I Server Manager går du till **verktyg** > **Group Policy Management**.
   
    ![Gå till Verktyg > Gruppera princip Managment](./media/deploy-access-panel-browser-extension/tools-gpm.png)
3. I den vänstra rutan i den **Group Policy Management** fönstret betrakta hierarkin organisationsenhet (OU) och avgör i vilken omfattning du vill tillämpa grupprincipen. Exempelvis kanske du vill välja en liten Organisationsenhet för att distribuera till några användare för testning, eller du kan välja en översta Organisationsenhet för att distribuera till hela organisationen.
   
   > [!NOTE]
   > Om du vill skapa eller redigera dina organisationsenheter (OU), växla tillbaka till Server Manager och gå till **verktyg** > **Active Directory-användare och datorer**.
   > 
   > 
4. När du har valt en Organisationsenhet, högerklicka och välj **skapa ett grupprincipobjekt i den här domänen och länka det här...**
   
    ![Skapa ett nytt GPO](./media/deploy-access-panel-browser-extension/create-gpo.png)
5. I den **nytt GPO** skriver ett namn på nytt grupprincipobjekt.
   
    ![Namn på det nya Grupprincipobjektet](./media/deploy-access-panel-browser-extension/name-gpo.png)
6. Högerklicka på grupprincipobjektet som du skapade och välj **redigera**.
   
    ![Redigera det nya Grupprincipobjektet](./media/deploy-access-panel-browser-extension/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Steg 3: Tilldela installationspaketet
1. Avgöra om du vill distribuera tillägget utifrån **Datorkonfiguration** eller **Användarkonfiguration**. När du använder [Datorkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), tillägget installeras på datorn oavsett vilken användare loggar in på den. Med [Användarkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), användare har tillägget installerat för dem, oavsett vilka datorer de loggar in på.
2. I den vänstra rutan i den **Redigeraren för Grupprinciphantering** fönster, gå till något av följande mappsökvägar, beroende på vilken typ av konfiguration du valde:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Högerklicka på **Programvaruinstallation**och välj sedan **New** > **paket...**
   
    ![Skapa en ny installationspaket för programvara](./media/deploy-access-panel-browser-extension/new-package.png)
4. Gå till den delade mappen som innehåller installationspaketet från [steg 1: skapa distributionsplatsen](#step-1-create-the-distribution-point), Välj MSI-filen och klicka på **öppna**.
   
   > [!IMPORTANT]
   > Om resursen finns på denna server måste du kontrollera att du använder MSI-filen via sökvägen till nätverk i stället för den lokala filsökvägen.
   > 
   > 
   
    ![Välj installationspaketet från den delade mappen.](./media/deploy-access-panel-browser-extension/select-package.png)
5. I den **distribuera programvara** fråga, Välj **tilldelad** för din distributionsmetod. Klicka sedan på **OK**.
   
    ![Tilldelad och sedan klicka på OK.](./media/deploy-access-panel-browser-extension/deployment-method.png)

Tillägget har nu distribuerats till den Organisationsenhet som du har valt. [Läs mer om Programvaruinstallation för Grupprincip.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Steg 4: Automatiskt – aktivera tillägget för Internet Explorer
Förutom att köra installationsprogrammet, måste alla tillägg för Internet Explorer uttryckligen aktiveras innan den kan användas. Följ stegen nedan för att aktivera åtkomst till panelen tillägget med hjälp av en Grupprincip:

1. I den **Redigeraren för Grupprinciphantering** går du till något av följande sökvägar, beroende på vilken typ av konfiguration som du valde i fönstret [steg3: tilldela installationspaketet](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Högerklicka på **lista över tillägg**, och välj **redigera**.
    ![Redigera lista över tillägg.](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)
3. I den **lista över tillägg** väljer **aktiverad**. Sedan, under den **alternativ** klickar du på **visa...** .
   
    ![Klicka på Aktivera och klicka sedan på Visa...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)
4. I den **Visa innehåll** och utför följande steg:
   
   1. För den första kolumnen (den **värdenamn** fältet), kopiera och klistra in följande klass-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. För den andra kolumnen (den **värdet** fältet), ange följande värde: `1`
   3. Klicka på **OK** att Stäng den **Visa innehåll** fönster.
      
      ![Fyll i värden som anges ovan.](./media/deploy-access-panel-browser-extension/show-contents.png)
5. Klicka på **OK** att tillämpa ändringarna och stänga den **lista över tillägg** fönster.

Tillägget ska nu vara aktiverad för datorer i den valda Organisationsenheten. [Läs mer om hur du använder grupprinciper för att aktivera eller inaktivera tillägg.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Steg 5 (valfritt): inaktivera ”Kom ihåg lösenord” fråga
När användare loggar in till webbplatser som använder tillägget för åtkomst-panelen, kan Internet Explorer indikera följande meddelandet som ber ”vill du lagra ditt lösenord”?

![Lösenordsprompt](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Om du vill hindra användarna från att se den här uppmaningen följer du stegen nedan för att förhindra automatisk komplettering från att komma ihåg lösenord:

1. I den **Redigeraren för Grupprinciphantering** fönster, gå till den sökväg som anges nedan. Den här inställningen är endast tillgänglig under **Användarkonfiguration**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Den inställning med namnet **aktiverar funktionen Komplettera automatiskt för användarnamn och lösenord för formulär**.
   
   > [!NOTE]
   > Tidigare versioner av Active Directory kan visa den här inställningen med namnet **Tillåt inte automatisk komplettering att spara lösenord**. Konfigurationen för den här inställningen skiljer sig från inställningen som beskrivs i den här självstudien.
   > 
   > 
   
    ![Kom ihåg att leta efter detta under inställningar.](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)
3. Högerklicka på inställningarna ovan och välj **redigera**.
4. I fönstret **aktiverar funktionen Komplettera automatiskt för användarnamn och lösenord för formulär**väljer **inaktiverad**.
   
    ![Välj inaktivera](./media/deploy-access-panel-browser-extension/disable-passwords.png)
5. Klicka på **OK** att tillämpa ändringarna och Stäng fönstret.

Användare kommer inte längre att kunna lagra sina autentiseringsuppgifter eller Använd automatisk komplettering för att komma åt tidigare lagrade autentiseringsuppgifter. Den här principen tillåter användare att fortsätta att använda automatisk komplettering för andra typer av fält, till exempel sökfält.

> [!WARNING]
> Om den här principen aktiveras när användare har valt att lagra vissa autentiseringsuppgifter, kommer den här principen *inte* ta bort de autentiseringsuppgifter som redan har lagrats.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Steg 6: Testa distributionen
Följ stegen nedan för att kontrollera om tillägget distributionen har lyckats:

1. Om du har distribuerat med **Datorkonfiguration**, logga in på en klientdator som hör till den Organisationsenhet som du valde i [steg 2: skapa grupprincipobjektet](#step-2-create-the-group-policy-object). Om du har distribuerat med **Användarkonfiguration**, se till att logga in som en användare som tillhör denna Organisationsenhet.
2. Det kan ta ett par logga moduler för grupprincipen ändras till fullständigt uppdatera med den här datorn. Om du vill tvinga uppdateringen, öppna en **kommandotolk** fönster och kör sedan följande kommando: `gpupdate /force`
3. Du måste starta om datorn för att installationen ska äga rum. Starttillstånd kan ta betydligt längre tid än vanligt när tillägget installeras.
4. Efter omstart, öppna **Internet Explorer**. I det övre högra hörnet i fönstret klickar du på **verktyg** (kugghjulsikonen) och välj sedan **Hantera tillägg**.
   
    ![Gå till Verktyg > Hantera tillägg](./media/deploy-access-panel-browser-extension/manage-add-ons.png)
5. I den **Hantera tillägg** fönstret kontrollerar du att den **Access Panel-tillägg** har installerats och att dess **Status** har ställts in på **aktiverad**.
   
    ![Kontrollera att tillägget för åtkomst-panelen är installerat och aktiverat.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="related-articles"></a>Relaterade artiklar
* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Felsöka Access Panel-tillägg för Internet Explorer](manage-access-panel-browser-extension.md)

