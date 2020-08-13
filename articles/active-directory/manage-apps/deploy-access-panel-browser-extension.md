---
title: Distribuera Azure Access panel-tillägget för Internet Explorer med hjälp av ett grup princip objekt | Microsoft Docs
description: Så här använder du grup princip för att distribuera Internet Explorer-tillägget för My Apps-portalen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31235936109361f0f15a071346baee128060375a
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88164996"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Gör så här: Distribuera åtkomst panels tillägget för Internet Explorer med grup princip

Den här självstudien visar hur du använder grup principer för att fjärrinstallera åtkomst panel tillägget för Internet Explorer på användarnas datorer. Det här tillägget krävs för Internet Explorer-användare som behöver logga in på appar som kon figurer ATS med [lösenordsbaserad enkel inloggning](sso-options.md#password-based-sso).

Det rekommenderas att administratörer automatiserar distributionen av tillägget. Annars måste användarna ladda ned och installera själva tillägget, vilket är känsligt för användar fel och kräver administratörs behörighet. Den här självstudien beskriver en metod för att automatisera program varu distributioner med hjälp av en grup princip. [Läs mer om grup principer.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Åtkomst panel tillägget är också tillgängligt för [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) och [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), men ingen av dem kräver administratörs behörighet för att installeras.

## <a name="prerequisites"></a>Förutsättningar

* Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till din domän.
* Du måste ha behörigheten "Redigera inställningar" för att kunna redigera grupprincip-objektet (GPO). Som standard har medlemmar i följande säkerhets grupper den här behörigheten: domän administratörer, företags administratörer och grupprincip Skapare ägare. [Läs mer.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Steg 1: skapa distributions platsen

Först måste du Placera installations paketet på en nätverks plats som kan nås av de datorer som du vill fjärrinstallera tillägget på. Det gör du genom att följa dessa steg:

1. Logga in på servern som administratör.
1. I fönstret **Serverhanteraren** går du till **filer och lagrings tjänster**.

    ![Öppna filer och lagrings tjänster](./media/deploy-access-panel-browser-extension/files-services.png)

1. Gå till fliken **resurser** . Klicka sedan på **uppgifter**  >  **Ny resurs...**

    ![Skärm bild som visar var du hittar en ny resurs på skärmen uppgifter](./media/deploy-access-panel-browser-extension/shares.png)

1. Slutför **guiden Ny resurs** och ange behörigheter för att säkerställa att den kan nås från användarnas datorer. [Lär dig mer om resurser.](https://technet.microsoft.com/library/cc753175.aspx)
1. Ladda ned följande Microsoft Windows Installer-paket (. msi-fil): [åtkomst panelen Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Kopiera installations paketet till önskad plats på resursen.

    ![Kopiera MSI-filen till resursen](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Kontrol lera att klient datorerna kan komma åt installations paketet från resursen.

## <a name="step-2-create-the-group-policy-object"></a>Steg 2: skapa ett grup princip objekt

1. Logga in på den server som är värd för din Active Directory Domain Services-installation (AD DS).
1. I Serverhanteraren går du till **verktyg**  >  **Grupprincip hantering**.

    ![Gå till verktyg > grupprincip hantering](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. I det vänstra fönstret i fönstret **Grupprincip hantering** visar du hierarkin för organisationsenheten (OU) och avgör i vilken omfattning du vill tillämpa grup principen. Du kan till exempel välja att välja en liten ORGANISATIONSENHET för att distribuera till några användare för testning, eller så kan du välja en ORGANISATIONSENHET på den översta nivån för att distribuera den till hela organisationen.

   > [!NOTE]
   > Om du vill skapa eller redigera organisations enheterna (OU) växlar du tillbaka till Serverhanteraren och går till **verktyg**  >  **Active Directory användare och datorer**.

1. När du har valt en ORGANISATIONSENHET högerklickar du på den och väljer **skapa ett grup princip objekt i den här domänen och länka det här..** .

    ![Skärm bild som visar alternativet Skapa ett nytt grup princip objekt](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. I den **nya GPO** -prompten skriver du ett namn för det nya Grupprincip-objektet.
1. Högerklicka på grupprincip-objektet som du skapade och välj **Redigera**.

## <a name="step-3-assign-the-installation-package"></a>Steg 3: tilldela installations paketet

1. Avgör om du vill distribuera tillägget baserat på **dator konfiguration** eller **användar konfiguration**. När du använder [dator konfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)installeras tillägget på datorn oavsett vilka användare som loggar in på den. Med [användar konfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)har användare installerat tillägget för dem oavsett vilka datorer de loggar in på.
1. I det vänstra fönstret i fönstret **redigeraren Grupprinciphantering** går du till någon av följande mappsökvägar, beroende på vilken typ av konfiguration du har valt:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Högerklicka på **program varu installation**och välj sedan **nytt**  >  **paket...**
1. Gå till den delade mappen som innehåller installations paketet från [steg 1: skapa distributions platsen](#step-1-create-the-distribution-point), Välj. msi-filen och klicka på **Öppna**.

   > [!IMPORTANT]
   > Om resursen finns på samma server kontrollerar du att du har åtkomst till. msi via nätverks fil Sök vägen, i stället för den lokala fil Sök vägen.

    ![Välj installations paketet i den delade mappen](./media/deploy-access-panel-browser-extension/select-package.png)

1. I meddelandet **distribuera program vara** väljer du **tilldelad** för distributions metoden. Klicka sedan på **OK**.

Tillägget distribueras nu till den ORGANISATIONSENHET som du har valt. [Läs mer om programvaruinstallation genom Windows Installer och grupprinciper.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Steg 4: aktivera tillägget automatiskt för Internet Explorer

Förutom att köra installations programmet måste alla tillägg för Internet Explorer uttryckligen aktive ras innan det kan användas. Följ stegen nedan för att aktivera åtkomst panel tillägget med grup princip:

1. I fönstret **redigeraren Grupprinciphantering** går du till någon av följande sökvägar, beroende på vilken typ av konfiguration du valde i [steg 3: tilldela installations paketet](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Högerklicka på **Lägg till-lista**och välj **Redigera**.

    ![Högerklicka på "Lägg till lista" och välj "redigera"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. I fönstret **Lägg till lista** väljer du **aktive rad**. Klicka sedan på Visa i avsnittet **alternativ** **...**.

    ![Klicka på Aktivera och sedan på Visa...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. I fönstret **Visa innehåll** utför du följande steg:

   1. För den första kolumnen (fältet **värde namn** ) kopierar du och klistrar in följande klass-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Ange följande värde för den andra kolumnen (fältet **värde** ): `1`
   1. Klicka på **OK** för att stänga fönstret **Visa innehåll** .

      ![Fyll i värdena enligt vad som anges i föregående steg](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klicka på **OK** för att tillämpa ändringarna och stänga **list** rutan för tillägg.

Tillägget ska nu vara aktiverat för datorerna i den valda ORGANISATIONSENHETen. [Läs mer om hur du använder grup principer för att aktivera eller inaktivera Internet Explorer-tillägg.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Steg 5 (valfritt): inaktivera meddelandet "kom ihåg lösen ord"

När användarna loggar in på webbplatser med åtkomst panel tillägget, kan Internet Explorer Visa följande fråga som frågar "vill du lagra lösen ordet?"

![Visar "vill du lagra lösen ordet..." visas](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Om du inte vill att användarna ska kunna se den här prompten följer du stegen nedan för att förhindra automatisk komplettering från att komma ihåg lösen ord:

1. I fönstret **redigeraren Grupprinciphantering** går du till sökvägen som anges nedan. Den här konfigurations inställningen är bara tillgänglig under **användar konfiguration**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Hitta inställningen **aktivera funktionen för automatisk komplettering för användar namn och lösen ord i formulär**.

   > [!NOTE]
   > Tidigare versioner av Active Directory kan visa den här inställningen med namnet **Tillåt inte automatisk komplettering för att spara lösen ord**. Konfigurationen för inställningen skiljer sig från inställningen som beskrivs i den här självstudien.

    ![Kom ihåg att leta efter detta under användar inställningar](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Högerklicka på inställningen ovan och välj **Redigera**.
1. I fönstret **aktivera funktionen för automatisk komplettering för användar namn och lösen ord i formulär väljer du** **inaktive rad**.

    ![Välj alternativet "inaktiverat" för funktionen Aktivera automatisk komplettering](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klicka på **OK** för att tillämpa ändringarna och Stäng fönstret.

Användarna kommer inte längre att kunna lagra sina autentiseringsuppgifter eller använda automatisk komplettering för att få åtkomst till tidigare lagrade autentiseringsuppgifter. Den här principen tillåter dock att användare fortsätter att använda automatisk komplettering för andra typer av formulär fält, t. ex. sökfält.

> [!WARNING]
> Om den här principen aktive ras efter att användare har valt att lagra autentiseringsuppgifter, tar den här principen *inte* bort de autentiseringsuppgifter som redan har lagrats.

## <a name="step-6-testing-the-deployment"></a>Steg 6: testa distributionen

Följ stegen nedan för att kontrol lera om tillägg distributionen lyckades:

1. Om du har distribuerat med **dator konfiguration**loggar du in på en klient dator som tillhör den organisationsenhet som du valde i [steg 2: skapa Grupprincip-objektet](#step-2-create-the-group-policy-object). Om du har distribuerat med hjälp av **användar konfiguration**, måste du logga in som en användare som tillhör organisationsenheten.
1. Det kan ta ett par inloggnings tillägg för grup princip ändringar som helt uppdateras med den här datorn. Om du vill framtvinga uppdateringen öppnar du ett **kommando tolks** fönster och kör följande kommando: `gpupdate /force`
1. Du måste starta om datorn för att installationen ska kunna ske. Start kan ta betydligt längre tid än vanligt medan tillägget installeras.
1. När du har startat om öppnar du **Internet Explorer**. I det övre högra hörnet i fönstret klickar du på **verktyg** (kugg hjuls ikonen) och väljer sedan **Hantera tillägg**.
1. I fönstret **Hantera tillägg** kontrollerar du att **åtkomst panel tillägget** har installerats och att dess **status** har angetts till **aktive rad**.

   ![Kontrol lera att åtkomst panels tillägget är installerat och aktiverat](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Mer information

* [Program åtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Felsöka åtkomst panels tillägget för Internet Explorer](manage-access-panel-browser-extension.md)
