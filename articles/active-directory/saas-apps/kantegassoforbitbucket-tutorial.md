---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för Bitbucket | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099212"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för Bitbucket

I den här självstudien får du lära dig hur du integrerar Kantega SSO för Bitbucket med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO för Bitbucket med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kantega SSO för Bitbucket.
* Du kan aktivera dina användare så att de automatiskt loggas in på Kantega SSO för Bitbucket (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kantega SSO för Bitbucket behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för Bitbucket enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO för Bitbucket stöder **SP och IDP** initierade SSO

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Lägga till Kantega SSO för Bitbucket från galleriet

Om du vill konfigurera integreringen av Kantega SSO för Bitbucket i Azure AD måste du lägga till Kantega SSO för Bitbucket från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Kantega SSO för Bitbucket från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Kantega SSO för Bitbucket**i sökrutan och välj **Kantega SSO för Bitbucket från** resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

    ![Kantega SSO för Bitbucket i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Kantega SSO för Bitbucket baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO för Bitbucket upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Kantega SSO för Bitbucket måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kantega SSO för Bitbucket Single Sign-On](#configure-kantega-sso-for-bitbucket-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Kantega SSO för Bitbucket-testanvändare](#create-kantega-sso-for-bitbucket-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kantega SSO för Bitbucket som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Kantega SSO för Bitbucket:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Kantega SSO för Bitbucket-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Kantega SSO för Bitbucket-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Kantega SSO för Bitbucket-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Bitbucket plugin som förklaras senare i handledningen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Kantega SSO för Bitbucket.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurera Kantega SSO för bitbucket enkel inloggning

1. I ett annat webbläsarfönster loggar du in på administratörsportalen Bitbucket som administratör.

1. Klicka på kuggen och klicka **på sök nya tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Sök **Kantega SSO för Bitbucket SAML & Kerberos** och klicka på **Installera** knappen för att installera den nya SAML plugin.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Plugin-installationen startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. I **SAML** SAML-sektionen. Välj **Azure Active Directory (Azure AD)** i listrutan Lägg till **identitetsprovider.**

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Välj prenumerationsnivå som **Grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Gör följande i avsnittet **Appegenskaper:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopiera **app-ID-URI-värdet** och använd det som **identifierare, svars-URL och inloggnings-URL** på avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Importera metadata:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Välj **Metadata-fil på min dator**och ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Namn och SSO-plats:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Lägg till namnet på identitetsprovidern i **identitetsproviderns namntextruta** (t.ex.

    b. Klicka på **Nästa**.

1. Verifiera signeringscertifikatet och klicka på **Nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Gör följande i avsnittet **Bitbucket-användarkonton:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Välj **Skapa användare i Bitbuckets interna katalog om det behövs** och ange lämpligt namn på gruppen för användare (kan vara flera nej. grupper åtskilda av kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. På avsnittet **Kända domäner för Azure AD** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Välj **Kända domäner** på sidans vänstra panel.

    b. Ange domännamn i textrutan **Kända domäner.**

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för Bitbucket.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Kantega SSO för Bitbucket**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Kantega SSO för Bitbucket**i programlistan .

    ![Den Kantega SSO för Bitbucket länken i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Skapa Kantega SSO för Bitbucket testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Bitbucket måste de etableras i Bitbucket. När det gäller Kantega SSO för Bitbucket är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen Bitbucket som administratör.

1. Klicka på inställningsikonen.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Klicka på **Användare**under avsnittet **Administration.**

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klicka på **Skapa användare**.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Gör följande på dialogrutan **Skapa användare:**

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Ange lösenordet för användaren i textrutan **Bekräfta lösenord.**

    f. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO för Bitbucket på åtkomstpanelen bör du automatiskt loggas in på Den Kantega SSO för Bitbucket som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

