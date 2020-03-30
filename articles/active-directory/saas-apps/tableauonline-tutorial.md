---
title: 'Självstudiekurs: Azure Active Directory-integrering med Tableau Online | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Tableau Online

I den här självstudien får du lära dig hur du integrerar Tableau Online med Azure Active Directory (Azure AD). När du integrerar Tableau Online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau Online.
* Gör att användarna automatiskt loggas in på Tableau Online med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Tableau Online enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau Online stöder **SP** initierade SSO
* När du har konfigurerat Tableau Online kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Lägga till Tablå online från galleriet

Om du vill konfigurera integreringen av Tableau Online i Azure AD måste du lägga till Tableau Online från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Tableau Online** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Tableau Online** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Tableau Online baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Tableau Online upprättas.

Om du vill konfigurera och testa Azure AD SSO med Tableau Online slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Tableau Online SSO](#configure-tableau-online-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Tableau Online-testanvändare](#create-tableau-online-test-user)** – om du vill ha en motsvarighet till B.Simon i Tableau Online som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Tableau Online:

1. Välj Enkel inloggning på sidan **Tableau** Online-programintegrering **på Azure-portalen**. [Azure portal](https://portal.azure.com/)

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Tableau Online Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Du får `<entityid>` värdet från avsnittet **Konfigurera Tableau Online** i den här självstudien. Enhets-ID-värdet är **Azure AD-identifierare** i **Avsnittet Konfigurera Tableau Online.**

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Tableau Online.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    BrittaSimon\@contoso.com till exempel

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Online.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Tableau Online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Tableau Online**i programlistan .

    ![Länken Tableau Online i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-tableau-online-sso"></a>Konfigurera Tableau Online SSO

1. I ett annat webbläsarfönster loggar du in på tableau Online-programmet. Gå till **Inställningar** och sedan **Autentisering**.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Om du vill aktivera SAML finns avsnittet **Autentiseringstyper under autentisering.** Markera **Aktivera en ytterligare autentiseringsmetod** och markera sedan KRYSSRUTAN **SAML.**

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Bläddra nedåt för att **importera metadatafil till tableau** online-avsnitt.  Klicka på Bläddra och importera metadatafilen som du har hämtat från Azure AD. Klicka sedan på **Använd**.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. I avsnittet **Matcha påståenden infogar** du motsvarande identitetsproviders kontrollnamn för **e-postadress,** **förnamn**och **efternamn**. Så här hämtar du den här informationen från Azure AD: 
  
    a. Gå till sidan **Tableau** Online-programintegrering i Azure-portalen.

    b. Klicka på redigeringsikonen i avsnittet **Användarattribut & anspråk.**

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection.png)

    c. Kopiera namnområdesvärdet för dessa attribut: givennamn, e-postadress och efternamn med hjälp av följande steg:

   ![Enkel azure AD-inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicka på värdet **user.givenname**

    e. Kopiera värdet från **namnområdestextrutan.**

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection2.png)

    f. Om du vill kopiera namnområdesvärdena för e-post och efternamn upprepar du ovanstående steg.

    g. Växla till Tableau Online-programmet och ange sedan avsnittet **Användarattribut & anspråk** på följande sätt:

    * E-post: **e-post** eller **användarnamn**

    * Förnamn: **förnamn**

    * Efternamn: **efternamn**

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Skapa testanvändare för Tableau Online

I det här avsnittet skapar du en användare som heter Britta Simon i Tableau Online.

1. Klicka på **Inställningar** i **tableau Online**och sedan **autentisering.** Bläddra nedåt till avsnittet **Hantera användare.** Klicka på **Lägg till användare** och sedan på Ange **e-postadresser**.
  
    ![Skapa en testanvändare för Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Välj **Lägg till användare för (SAML) autentisering**. Lägg **Enter email addresses** till britta.simon\@contoso.com
  
    ![Skapa en testanvändare för Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Online på åtkomstpanelen ska du automatiskt loggas in på den Tabell online som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)