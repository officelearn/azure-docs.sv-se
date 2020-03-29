---
title: Distribuera Azure Access Panel-tillägg för IE med hjälp av ett GPO | Microsoft-dokument
description: Så här använder du grupprincipen för att distribuera Internet Explorer-tillägget för portalen Mina appar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807690"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Så här distribuerar du tillägget till åtkomstpanelen för Internet Explorer med hjälp av grupprinciper

Den här självstudien visar hur du använder grupprincipen för att fjärrinstallation av tillägget till åtkomstpanelen för Internet Explorer på användarnas datorer. Det här tillägget krävs för Internet Explorer-användare som behöver logga in på appar som är konfigurerade med hjälp av [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md#password-based-sso).

Vi rekommenderar att administratörer automatiserar distributionen av det här tillägget. Annars måste användarna själva hämta och installera tillägget, som är benägna att användarfel och kräver administratörsbehörighet. Den här självstudien täcker en metod för att automatisera programdistributioner med hjälp av grupprinciper. [Läs mer om grupprinciper.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Tillägget Access Panel är också tillgängligt för [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) och [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), varav ingen kräver administratörsbehörighet för att installera.

## <a name="prerequisites"></a>Krav

* Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till domänen.
* Du måste ha behörigheten "Redigera inställningar" för att kunna redigera grupprincipobjektet. Som standard har medlemmar i följande säkerhetsgrupper den här behörigheten: Domänadministratörer, företagsadministratörer och ägare av grupprincipskapare. [Läs mer.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Steg 1: Skapa distributionsplatsen

Först måste du placera installationspaketet på en nätverksplats som kan nås av de datorer som du vill fjärrinstallation av tillägget på. Det gör du genom att följa dessa steg:

1. Logga in på servern som administratör.
1. Gå till Filer och **Lagringstjänster**i fönstret **Serverhanteraren** .

    ![Öppna filer och lagringstjänster](./media/deploy-access-panel-browser-extension/files-services.png)

1. Gå till fliken **Resurser.** Klicka sedan på **Uppgifter** > **ny resurs...**

    ![Skärmbild visar var ny del hittar på skärmen Uppgifter](./media/deploy-access-panel-browser-extension/shares.png)

1. Fyll i **guiden Ny resurs** och ange behörigheter för att säkerställa att den kan nås från användarnas datorer. [Läs mer om resurser.](https://technet.microsoft.com/library/cc753175.aspx)
1. Hämta följande Microsoft Windows Installer-paket (.msi-fil): [Tillägg till åtkomstpanelen.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Kopiera installationspaketet till en önskad plats på resursen.

    ![Kopiera MSI-filen till resursen](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Kontrollera att klientdatorerna kan komma åt installationspaketet från resursen.

## <a name="step-2-create-the-group-policy-object"></a>Steg 2: Skapa grupprincipobjektet

1. Logga in på servern som är värd för AD DS-installationen (Active Directory Domain Services).
1. Gå till **Verktyg** > **grupprinciphantering**i Serverhanteraren .

    ![Gå till Verktyg > Grupprinciphantering](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. I den vänstra rutan i fönstret **Grupprinciphantering** visar du organisationsenhetens hierarki (OU) och bestämmer vid vilket scope du vill använda grupprincipen. Du kan till exempel välja en liten organisationsenhet som ska distribueras till några användare för testning, eller så kan du välja en organisationsenhet på den högsta nivån som ska distribueras till hela organisationen.

   > [!NOTE]
   > Om du vill skapa eller redigera organisationsenheterna växlar du tillbaka till Serverhanteraren och går till **Verktyg** > **för Active Directory – användare och datorer**.

1. När du har valt en organisationsenhet högerklickar du på den och väljer **Skapa ett gemensamt användarobjekt i den här domänen och länka det här...**

    ![Skärmbild visar alternativet Skapa ett nytt gpo](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Skriv in ett namn på det nya grupprincipobjektet i den nya grupprincipobjektet. **New GPO**
1. Högerklicka på det grupprincipobjekt som du skapade och välj **Redigera**.

## <a name="step-3-assign-the-installation-package"></a>Steg 3: Tilldela installationspaketet

1. Ta reda på om du vill distribuera tillägget baserat på **datorkonfiguration** eller **användarkonfiguration**. När du använder [datorkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)installeras tillägget på datorn oavsett vilka användare som loggar in på den. Med [användarkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)har användarna tillägget installerat för dem oavsett vilka datorer de loggar in på.
1. I den vänstra rutan i fönstret Redigerare för **grupprinciphantering** går du till någon av följande mappsökvägar, beroende på vilken typ av konfiguration du valde:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Högerklicka på **Programvaruinstallation**och välj sedan **Nytt** > **paket...**
1. Gå till den delade mappen som innehåller installationspaketet från [steg 1: Skapa distributionsplatsen](#step-1-create-the-distribution-point), markera MSI-filen och klicka på **Öppna**.

   > [!IMPORTANT]
   > Om resursen finns på samma server kontrollerar du att du ansluter till MSI via sökvägen till nätverksfilen i stället för den lokala filsökvägen.

    ![Välj installationspaketet från den delade mappen](./media/deploy-access-panel-browser-extension/select-package.png)

1. I frågan **distribuera programvara** väljer du **Tilldelad** för distributionsmetoden. Klicka sedan på **OK**.

Tillägget distribueras nu till den organisationsenhet som du har valt. [Läs mer om programvaruinstallation av grupprinciper.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Steg 4: Aktivera tillägget för Internet Explorer automatiskt

Förutom att köra installationsprogrammet måste varje tillägg för Internet Explorer uttryckligen aktiveras innan det kan användas. Följ stegen nedan för att aktivera tillägg för åtkomstpanelen med hjälp av grupprincip:

1. I fönstret Redigerare för **grupprinciphantering** går du till någon av följande sökvägar, beroende på vilken typ av konfiguration du valde i [steg 3: Tilldela installationspaketet:](#step-3-assign-the-installation-package)

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Högerklicka på **Tilläggslista**och välj **Redigera**.

    ![Högerklicka på "Lägg till lista" och välj "Redigera"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Välj **Aktiverad**i fönstret **Tilläggslista** . Klicka sedan på **Visa...** under avsnittet **Alternativ.**

    ![Klicka på Aktivera och sedan på Visa...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Gör följande i fönstret **Visa innehåll:**

   1. För den första kolumnen (fältet **Värdenamn)** kopierar och klistrar du in följande klass-ID:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. För den andra kolumnen (fältet **Värde)** skriver du följande värde:`1`
   1. Stäng fönstret **Visa innehåll** genom att klicka på **OK.**

      ![Fylla i de värden som anges i föregående steg](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klicka på **OK** om du vill tillämpa ändringarna och stänga fönstret **Lägg till lista.**

Tillägget ska nu aktiveras för maskinerna i den valda organisationsenheten. [Läs mer om hur du använder grupprinciper för att aktivera eller inaktivera Internet Explorer-tillägg.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Steg 5 (valfritt): Inaktivera uppmaningen "Kom ihåg lösenord"

När användare loggar in på webbplatser med tillägget för åtkomstpanelen kan följande uppmaning visas i Internet Explorer med följande frågar "Vill du lagra ditt lösenord?"

![Visar "Vill du lagra ditt lösenord..." Snabb](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Om du vill förhindra att användarna ser den här uppmaningen följer du stegen nedan för att förhindra att du automatiskt slutför lösenord:

1. Gå till sökvägen nedan i fönstret **Redigerare för grupprinciphantering.** Den här konfigurationsinställningen är endast tillgänglig under **Användarkonfiguration**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Hitta inställningen **Aktivera funktionen komplettera automatiskt för användarnamn och lösenord i formulär**.

   > [!NOTE]
   > Tidigare versioner av Active Directory kan lista den här inställningen med namnet **Tillåt inte att automatiskt slutföra lösenord**. Konfigurationen för den inställningen skiljer sig från den inställning som beskrivs i den här självstudien.

    ![Kom ihåg att leta efter detta under Användarinställningar](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Högerklicka på ovanstående inställning och välj **Redigera**.
1. Välj **Inaktivera**i fönstret **Aktivera funktionen Komplettera automatiskt för användarnamn och lösenord i formulär**.

    ![Välj alternativet "Inaktiverad" för funktionen för automatisk komplettering](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klicka på **OK** om du vill tillämpa ändringarna och stänga fönstret.

Användare kan inte längre lagra sina autentiseringsuppgifter eller använda komplettera automatiskt för att komma åt tidigare lagrade autentiseringsuppgifter. Den här principen gör det dock möjligt för användare att fortsätta att använda automatiskt komplettera för andra typer av formulärfält, till exempel sökfält.

> [!WARNING]
> Om den här principen är aktiverad när användare har valt att lagra vissa autentiseringsuppgifter, kommer den här principen *inte* att rensa de autentiseringsuppgifter som redan har lagrats.

## <a name="step-6-testing-the-deployment"></a>Steg 6: Testa distributionen

Följ stegen nedan för att kontrollera om tilläggsdistributionen lyckades:

1. Om du har distribuerat med **datorkonfiguration**loggar du in på en klientdator som tillhör den organisationsenhet som du valde i [steg 2: Skapa grupprincipobjektet](#step-2-create-the-group-policy-object). Om du har distribuerat med **användarkonfiguration**måste du logga in som en användare som tillhör organisationsenheten.
1. Det kan ta ett par inloggningar för grupprincipändringarna att uppdateras fullständigt med den här datorn. Om du vill tvinga fram uppdateringen öppnar du ett **kommandotolksfönster** och kör följande kommando:`gpupdate /force`
1. Du måste starta om datorn för att installationen ska kunna ske. Uppstart kan ta betydligt längre tid än vanligt medan tillägget installeras.
1. Öppna **Internet Explorer**när du har startat om . Klicka på **Verktyg** (kugghjulsikonen) längst upp till höger i fönstret och välj sedan **Hantera tillägg**.
1. I fönstret **Hantera tillägg** kontrollerar du att tillägget **till åtkomstpanelen** har installerats och att dess **status** har angetts till **Aktiverad**.

   ![Kontrollera att tillägg till åtkomstpanelen är installerat och aktiverat](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Läs mer

* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Felsöka tillägg till åtkomstpanelen för Internet Explorer](manage-access-panel-browser-extension.md)
