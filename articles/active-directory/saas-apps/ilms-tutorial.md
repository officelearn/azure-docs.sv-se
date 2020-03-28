---
title: 'Självstudiekurs: Azure Active Directory-integrering med iLMS | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944024"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Självstudiekurs: Integrera iLMS med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar iLMS med Azure Active Directory (Azure AD). När du integrerar iLMS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till iLMS.
* Aktivera dina användare så att de automatiskt loggas in på iLMS med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* iLMS enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. iLMS stöder **SP och IDP** initierade SSO

## <a name="adding-ilms-from-the-gallery"></a>Lägga till iLMS från galleriet

Om du vill konfigurera integreringen av iLMS i Azure AD måste du lägga till iLMS från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **iLMS** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **iLMS** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med iLMS med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i iLMS.

Så här konfigurerar och testar du Azure AD SSO med iLMS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera iLMS SSO](#configure-ilms-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa iLMS-testanvändare](#create-ilms-test-user)** – om du vill ha en motsvarighet till Britta Simon i iLMS som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **iLMS** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge på sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare klistrar** du in det identifierare värde som du **kopierar** från avsnittet **Tjänstprovider** i SAML-inställningar i iLMS-administratörsportalen.

    b. I textrutan **Svara URL** klistrar du in värdet **Slutpunkt (URL)** som du kopierar från avsnittet **Tjänsteleverantör** i SAML-inställningar i iLMS-administratörsportalen med följande mönster`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Sign-on URL** klistrar du in värdet **slutpunkt (URL)** som du kopierar från avsnittet **Tjänsteleverantör** i SAML-inställningar i iLMS-administratörsportalen som`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. För att aktivera JIT-etablering förväntar sig iLMS-programmet SAML-påståendena i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    > [!NOTE]
    > Du måste aktivera **Skapa oerkändt användarkonto** i iLMS för att mappa dessa attribut. Följ instruktionerna [här](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) för att få en uppfattning om attributkonfigurationen.

1. Utöver ovanstående förväntar sig iLMS-programmet att få fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | --------|------------- |
    | Division | user.department |
    | regionen | användare.tillstånd |
    | avdelning | user.jobtitle |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera iLMS.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ilms-sso"></a>Konfigurera iLMS SSO

1. I ett annat webbläsarfönster loggar du in på **iLMS-administratörsportalen** som administratör.

2. Klicka på **SSO:SAML** under **fliken Inställningar** för att öppna SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/1.png)

3. Expandera avsnittet **Tjänsteprovider** och kopiera värdet **identifierare** och **slutpunkt (URL).**

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/2.png) 

4. Klicka på Importera metadata under avsnittet **Identitetsprovider.** **Identity Provider**

5. Välj filen **Federation Metadata** som hämtats från Azure-portalen från avsnittet **SAML-signeringscertifikat.**

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Om du vill aktivera JIT-etablering för att skapa iLMS-konton för ej identifiera användare följer du stegen nedan:

    a. Markera **Skapa oerkändt användarkonto**.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mappa attributen i Azure AD med attributen i iLMS. Ange attributnamnet eller standardvärdet i attributkolumnen.

    c. Gå till fliken **Affärsregler** och gör följande:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/5.png)

    d. Markera **Skapa oerkända regioner, divisioner och avdelningar** för att skapa regioner, divisioner och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.

    e. Kontrollera **Uppdatera användarprofil under inloggning** för att ange om användarens profil ska uppdateras med varje enkel inloggning.

    f. Om alternativet **Uppdatera tomma värden för icke obligatoriska fält i användarprofilen** är markerat, kommer även valfria profilfält som är tomma vid inloggning att användarens iLMS-profil innehåller tomma värden för dessa fält.

    g. Kontrollera **Skicka felmeddelande e-post** och ange e-post till den användare där du vill få felmeddelandet e-post.

7. Klicka på knappen **Spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till iLMS.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **iLMS**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-ilms-test-user"></a>Skapa iLMS-testanvändare

Programmet stöder Just in time användaretablering och efter autentisering användare skapas i programmet automatiskt. JIT fungerar om du har klickat på kryssrutan **Skapa oerkända användarkonto** under KONFIGURATIONSINSTÄLLNINGEN FÖR SAML på iLMS-administratörsportalen.

Om du behöver skapa en användare manuellt följer du stegen nedan:

1. Logga in på iLMS-företagets webbplats som administratör.

2. Klicka på **Registrera användare** under **fliken Användare** för att öppna Sidan **Registrera användare.**

   ![Lägga till medarbetare](./media/ilms-tutorial/3.png)

3. Gör följande på sidan **Registrera användare.**

    ![Lägga till medarbetare](./media/ilms-tutorial/create_testuser_add.png)

    a. Skriv förnamnet som Britta i textrutan **Förnamn.**

    b. Skriv efternamnet som Simon i textrutan **Efternamn.**

    c. Skriv **Email ID** e-postadressen för användaren som BrittaSimon@contoso.com.

    d. Välj värdet för region i listrutan **Region.**

    e. Välj värdet för division i listrutan **Division.**

    f. Välj värdet för avdelningen i listrutan **Avdelning.**

    g. Klicka på **Spara**.

    > [!NOTE]
    > Du kan skicka registreringspost till användaren genom att markera kryssrutan **Skicka registreringsbrev.**

### <a name="test-sso"></a>Testa SSO

När du väljer iLMS-panelen på åtkomstpanelen ska du automatiskt loggas in på den iLMS som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
