---
title: 'Självstudiekurs: Azure Active Directory-integrering med Peakon | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094728"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Självstudiekurs: Azure Active Directory-integrering med Peakon

I den här självstudien får du lära dig hur du integrerar Peakon med Azure Active Directory (Azure AD).
Genom att integrera Peakon med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Peakon.
* Du kan aktivera dina användare så att de automatiskt loggas in på Peakon (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Peakon behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Peakon enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Peakon stöder **SP** och **IDP** initierad SSO

## <a name="adding-peakon-from-the-gallery"></a>Lägga till Peakon från galleriet

Om du vill konfigurera integreringen av Peakon i Azure AD måste du lägga till Peakon från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Peakon från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Peakon**i sökrutan och välj **Peakon** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Peakon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Peakon baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Peakon upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Peakon måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Peakon Single Sign-On](#configure-peakon-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Peakon-testanvändare](#create-peakon-test-user)** – om du vill ha en motsvarighet till Britta Simon i Peakon som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Peakon:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Peakon-programintegration**. **Peakon**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Peakon-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://app.peakon.com/saml/<companyid>/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://app.peakon.com/saml/<companyid>/assert`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Peakon-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.peakon.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med url:en för faktisk identifierare och svar som förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Peakon.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-peakon-single-sign-on"></a>Konfigurera Peakon enkel inloggning

1. Logga in på Peakon som administratör i ett annat webbläsarfönster.

2. Klicka på **Konfiguration**i menyraden till vänster på sidan och navigera sedan till **Integrationer**.

    ![Den Config](./media/peakon-tutorial/tutorial_peakon_config.png)

3. På **integrationssidan** klickar du på **Enkel inloggning**.

    ![Den enda](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Under Avsnittet **Enkel inloggning** klickar du på **Aktivera**.

    ![Den aktiverade](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Gör följande på avsnittet **Enkel inloggning för anställda som använder SAML:**

    ![Den saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Klistra in värdet **för inloggnings-URL**i textrutan **För inloggningsadress** , som du har kopierat från Azure-portalen.

    b. Klistra in värdet **för url för utloggning för utloggning**i **textrutan För utloggningsadress** , som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** om du vill överföra certifikatet som du har hämtat från Azure-portalen till rutan Certifikat.

    d. Klicka på **ikonen** om du vill kopiera **entitets-ID:t** och klistra in i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    e. Klicka på **ikonen** om du vill kopiera **svars-URL:en (ACS)** och klistra in i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    f. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Peakon.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Peakon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Peakon**i programlistan .

    ![Peakon-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-peakon-test-user"></a>Skapa Peakon-testanvändare

För att Azure AD-användare ska kunna logga in på Peakon måste de etableras i Peakon.  
När det gäller Peakon är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Peakon-företagswebbplats som administratör.

2. Klicka på **Konfiguration**i menyraden till vänster på sidan och navigera sedan till **Anställda**.

    ![Den anställde](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Klicka på **Lägg till medarbetare**längst upp till höger på sidan .

      ![Lägg till medarbetare](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Gör följande på dialogrutan **Ny anställd:**

     ![Den nya medarbetaren](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Skriv förnamn som **Britta** och efternamn som **simon**i textrutan **Namn** .

    b. Skriv e-postadressen som **Brittasimon\@contoso.com**i **textrutan e-post** .

    c. Klicka på **Skapa medarbetare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Topon-panelen på åtkomstpanelen ska du automatiskt loggas in på den Peakon som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

