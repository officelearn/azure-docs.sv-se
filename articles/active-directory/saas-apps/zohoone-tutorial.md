---
title: 'Självstudier: Azure Active Directory-integrering med en Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho en.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: c731919baf3acc8cedfb31c088f9a0a12791251c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717985"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudier: Azure Active Directory-integrering med en Zoho

I den här självstudien får du lära dig hur du integrerar en Zoho med Azure Active Directory (AD Azure).
Integrera en Zoho med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till en Zoho.
* Du kan aktivera användarna att vara automatiskt inloggad till en Zoho (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med en Zoho, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Zoho en enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* En Zoho stöder **SP** och **IDP** -initierad SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Att lägga till en Zoho från galleriet

Om du vill konfigurera integreringen av en Zoho i Azure AD, som du behöver lägga till en Zoho från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till en Zoho från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Zoho en**väljer **Zoho en** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![En Zoho i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zoho en baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i en Zoho upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med en Zoho, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zoho en enkel inloggning](#configure-zoho-one-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en Zoho testanvändare](#create-zoho-one-test-user)**  – du har en motsvarighet för Britta Simon i Zoho en som är kopplad till Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med en Zoho, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Zoho en** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Zoho en domän och URL: er med enkel inloggning för information](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du in en URL: `one.zoho.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Föregående **svars-URL** värdet är inte verkliga. Du får den `<saml-identifier>` värdet från #step4 av **konfigurera Zoho en enkel inloggning** som beskrivs senare i självstudien.

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://one.zoho.com`

5. Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:


    ![Zoho en domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Föregående **inloggnings-URL** värdet är inte verkliga. Du kommer att uppdatera värdet med faktiska inloggnings-URL: en från den **konfigurera Zoho en enkel inloggning** som beskrivs senare i självstudien. 

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **ställa in en Zoho** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurera Zoho en enkel inloggning

1. I ett annat webbläsarfönster, loggar du in på webbplatsen Zoho ett företag som administratör.

2. På den **organisation** fliken, klickar du på **installationsprogrammet** under **SAML-autentisering**.

    ![En Zoho org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. På sidan popup utför du följande steg:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. I den **inloggning URL** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I den **URL: en för utloggning** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** att ladda upp den **certifikat (Base64)** som du har hämtat från Azure-portalen.

    d. Klicka på **Spara**.

4. När du har sparat inställningarna för SAML-autentisering, kopiera den **SAML-ID** värde och med den **svars-URL** i stället för `<saml-identifier>`, till exempel `https://accounts.zoho.com/samlresponse/one.zoho.com` och klistra in det genererade värdet i den **Svars-URL** textrutan under **SAML grundkonfiguration** avsnittet.

    ![Zoho One saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Gå till den **domäner** fliken och klicka sedan på **Lägg till domän**.

    ![Zoho One domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. På den **Lägg till domän** utför följande steg:

    ![Zoho en Lägg till domän](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. I den **domännamn** textrutan Ange domän t.ex. contoso.com.

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du lägger till den domän Följ [dessa](https://www.zoho.com/one/help/admin-guide/domain-verification.html) steg för att verifiera din domän. När domänen har verifierats, använda ditt domännamn i **inloggnings-URL** i **SAML grundkonfiguration** avsnitt i Azure-portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till en Zoho.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Zoho en**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zoho en**.

    ![Zoho en länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zoho-one-test-user"></a>Skapa en Zoho testanvändare

Om du vill aktivera Azure AD-användare att logga in på en Zoho, måste de etableras i en Zoho. I en Zoho är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Zoho en som en administratör.

2. På den **användare** fliken, klicka på **användaren logotyp**.

    ![Zoho One user](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. På den **Lägg till användare** utför följande steg:

    ![En Zoho lägga till användare](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. I **namn** text, ange namnet på användaren som **Britta simon**.
    
    b. I **e-postadress** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    >[!Note]
    >Välj din verifierade domän i listan för domänen.

    c. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på den Zoho en panelen i åtkomstpanelen, bör det vara loggas in automatiskt till den Zoho en som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

