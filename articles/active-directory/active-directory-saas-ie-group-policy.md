---
title: Distribuera Azure Access panelen-tillägg för Internet Explorer med hjälp av ett grupprincipobjekt | Microsoft Docs
description: Hur du använder grupprinciper för att distribuera Internet Explorer-tillägget för Mina appar portalen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d719a31700e8d693dd7edb7de603682daae14ea
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308544"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip
Den här kursen visar hur du använder grupprinciper för att fjärrinstallera åtkomstpanelen-tillägg för Internet Explorer på användarnas datorer. Det här tillägget krävs för Internet Explorer-användare som behöver logga in på appar som är konfigurerade med [lösenordsbaserade enkel inloggning](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on).

Vi rekommenderar att administratörer automatisera distributionen av det här tillägget. I annat fall behöver användarna ladda ned och installera tillägget själva, som är lätt fel och måste ha administratörsbehörighet. Den här självstudiekursen beskriver en metod för att automatisera programvarudistributioner med hjälp av Grupprincip. [Läs mer om grupprinciper.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Tillägget åtkomstpanelen är också tillgängligt för [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) och [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), som inte kräver administratörsbehörighet för att installera.

## <a name="prerequisites"></a>Förutsättningar
* Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit användarnas datorer till domänen.
* Du måste ha behörigheten ”Redigera inställningar” så här redigerar du den grupprincipobjektet (GPO). Som standard medlemmar i följande säkerhetsgrupper har denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Steg 1: Skapa distributionsplatsen.
Först måste du placera installer-paketet på en nätverksplats som kan användas av datorer som du vill fjärrinstallera tillägget på. Det gör du genom att följa dessa steg:

1. Logga in på servern som en administratör
2. I den **Serverhanteraren** fönster, gå till **filer och lagringstjänster**.
   
    ![Öppna filer och lagringstjänster](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Gå till den **resurser** fliken. Klicka på **uppgifter** > **ny resurs...**
   
    ![Öppna filer och lagringstjänster](./media/active-directory-saas-ie-group-policy/shares.png)
4. Slutför den **ny resurs Guide** och ange behörigheter så att den kan nås från användarnas datorer. [Mer information om resurser.](https://technet.microsoft.com/library/cc753175.aspx)
5. Hämta följande Microsoft Windows Installer-paketet (MSI-fil): [åtkomst panelen Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
6. Kopiera installationspaketet till önskad plats för resursen.
   
    ![Kopiera MSI-filen till resursen.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Kontrollera att dina klientdatorer har tillgång till installationspaketet från resursen. 

## <a name="step-2-create-the-group-policy-object"></a>Steg 2: Skapa grupprincipobjektet
1. Logga in på servern som är värd för Active Directory Domain Services (AD DS)-installationen.
2. I Serverhanteraren, gå till **verktyg** > **Grupprinciphantering**.
   
    ![Gå till Verktyg > Gruppera Policy Management](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. I den vänstra rutan i **Grupprinciphantering** fönstret Visa organisationsenheten (OU) hierarkin och avgöra vilka definitionsområdet du vill tillämpa grupprincipen. Exempelvis kanske du vill välja en liten Organisationsenhet för att distribuera till ett fåtal användare för att testa eller du kan välja en översta Organisationsenhet för att distribuera till hela organisationen.
   
   > [!NOTE]
   > Om du vill skapa eller redigera dina organisationsenheter (OU), växla tillbaka till Server Manager och gå till **verktyg** > **Active Directory-användare och datorer**.
   > 
   > 
4. När du har valt en Organisationsenhet, högerklicka och välj **skapa ett grupprincipobjekt i den här domänen och länka det här...**
   
    ![Skapa ett nytt GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. I den **nytt GPO** skriver ett namn på nytt grupprincipobjekt.
   
    ![Namn på det nya Grupprincipobjektet](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Högerklicka på grupprincipobjektet som du skapade och välj **redigera**.
   
    ![Redigera det nya Grupprincipobjektet](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Steg 3: Tilldela installationspaketet
1. Avgöra om du vill distribuera tillägget baserat på **Datorkonfiguration** eller **Användarkonfiguration**. När du använder [Datorkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), tillägget har installerats på datorn oavsett vilka användare loggar in på den. Med [Användarkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), användare ha filnamnstillägget installerats för dem oavsett vilka datorer de loggar in på.
2. I den vänstra rutan i **Redigeraren för Grupprinciphantering** fönster, gå till något av följande mappsökvägar, beroende på vilken typ av du har valt:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Högerklicka på **Programvaruinstallation**och välj **ny** > **paket...**
   
    ![Skapa en ny installationspaket för programvara](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Gå till den delade mappen som innehåller installationspaketet från [steg 1: skapa distributionsplatsen](#step-1-create-the-distribution-point), Välj MSI-filen och klicka på **öppna**.
   
   > [!IMPORTANT]
   > Om resursen finns på den här samma server, kontrollerar du att du använder .msi via nätverket filsökväg i stället för den lokala filsökvägen.
   > 
   > 
   
    ![Välj installationspaketet från den delade mappen.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. I den **distribuera programvara** fråga, Välj **tilldelad** för din distributionsmetod. Klicka sedan på **OK**.
   
    ![Välj tilldelad och klicka sedan på OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Tillägget har nu distribuerats till Organisationsenheten som du har valt. [Läs mer om Programvaruinstallation för Grupprincip.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Steg 4: Auto-aktivera tillägget för Internet Explorer
Förutom att köra installationsprogrammet, måste alla tillägg för Internet Explorer uttryckligen aktiveras innan den kan användas. Följ stegen nedan för att aktivera åtkomst panelen tillägget med hjälp av Grupprincip:

1. I den **Redigeraren för Grupprinciphantering** fönster, gå till något av följande sökvägar, beroende på vilken typ av du valde i [steg3: tilldela installationspaketet](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Högerklicka på **lista över tillägg**, och välj **redigera**.
    ![Redigera lista över tillägg.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. I den **lista över tillägg** väljer **aktiverad**. Sedan, under den **alternativ** klickar du på **visa...** .
   
    ![Klicka på Aktivera och klicka sedan på Visa...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. I den **Visa innehåll** fönster, utför följande steg:
   
   1. För den första kolumnen (den **värdenamn** fältet), kopiera och klistra in följande klass-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. För den andra kolumnen (den **värdet** fältet), anger följande värde: `1`
   3. Klicka på **OK** att stänga den **Visa innehåll** fönster.
      
      ![Fyll i värdena som anges ovan.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Klicka på **OK** att tillämpa ändringarna och stänga den **lista över tillägg** fönster.

Tillägget bör nu aktiveras för datorer i den valda Organisationsenheten. [Mer information om hur du använder grupprinciper för att aktivera eller inaktivera tillägg för Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Steg 5 (valfritt): inaktivera ”Kom ihåg lösenord” fråga
När användare logga in på webbplatser med åtkomst till Kontrollpanelen, kan Internet Explorer visa följande meddelande som ber ”vill du att lagra lösenordet”?

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Om du vill förhindra att användarna kan se den här uppmaningen följer du stegen nedan för att förhindra automatisk komplettering från att komma ihåg lösenord:

1. I den **Redigeraren för Grupprinciphantering** fönster, gå till sökvägen som anges nedan. Den här inställningen är endast tillgängliga under **Användarkonfiguration**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Den inställning som heter **aktivera funktionen för automatisk komplettering för användarnamn och lösenord för formulär för**.
   
   > [!NOTE]
   > Tidigare versioner av Active Directory kan visa en lista med den här inställningen med namnet **tillåter inte automatisk komplettering att spara lösenord**. Konfigurationen för den här inställningen skiljer sig från inställningen som beskrivs i den här kursen.
   > 
   > 
   
    ![Kom ihåg att leta efter detta under inställningar.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Högerklicka på inställningarna ovan och välj **redigera**.
4. I fönstret **aktivera funktionen för automatisk komplettering för användarnamn och lösenord för formulär för**väljer **inaktiverade**.
   
    ![Välj inaktivera](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Klicka på **OK** att tillämpa ändringarna och stänga fönstret.

Användare kommer inte längre att kunna lagra sina autentiseringsuppgifter eller Använd automatisk komplettering för att komma åt tidigare lagrade autentiseringsuppgifter. Den här principen tillåter användare att fortsätta att använda automatisk komplettering för andra typer av fält, till exempel sökfält.

> [!WARNING]
> Om den här principen är aktiverad när användare har valt att lagra vissa autentiseringsuppgifter, kommer den här principen *inte* avmarkerar du de autentiseringsuppgifter som redan har lagrats.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Steg 6: Testa distributionen
Följ stegen nedan för att kontrollera om tillägget distributionen har lyckats:

1. Om du har distribuerat med **Datorkonfiguration**, logga in på en klientdator som hör till den Organisationsenhet som du valde i [steg 2: skapa grupprincipobjektet](#step-2-create-the-group-policy-object). Om du har distribuerat med **Användarkonfiguration**, se till att logga in som en användare som tillhör denna Organisationsenhet.
2. Det kan ta ett par logga moduler för grupprincipen ändras till fullständigt uppdatera till den här datorn. Om du vill tvinga uppdateringen, öppna en **kommandotolk** fönster och kör sedan följande kommando: `gpupdate /force`
3. Du måste starta om datorn för att installationen ska kunna utföras. Autostart kan ta betydligt längre tid än vanligt när tillägget installeras.
4. Efter omstart, öppna **Internet Explorer**. Klicka på det övre högra hörnet i fönstret **verktyg** (kugghjulsikonen) och välj sedan **Hantera tillägg**.
   
    ![Gå till Verktyg > Hantera tillägg](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. I den **Hantera tillägg** fönster, kontrollerar du att den **åtkomst panelen tillägget** har installerats och att dess **Status** har ställts in på **aktiverad**.
   
    ![Kontrollera att tillägget för åtkomst-panelen är installerat och aktiverat.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Felsökning av Access panelen-tillägg för Internet Explorer](active-directory-saas-ie-troubleshooting.md)

