---
title: 'Självstudiekurs: Azure Active Directory-integrering med Fluxx Labs | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102379"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Självstudiekurs: Azure Active Directory-integrering med Fluxx Labs

I den här självstudien får du lära dig hur du integrerar Fluxx Labs med Azure Active Directory (Azure AD).
Genom att integrera Fluxx Labs med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Fluxx Labs.
* Du kan aktivera dina användare så att de automatiskt loggas in på Fluxx Labs (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Fluxx Labs behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Fluxx Labs enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Fluxx Labs stöder **IDP** initierad SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Lägga till Fluxx Labs från galleriet

Om du vill konfigurera integreringen av Fluxx Labs i Azure AD måste du lägga till Fluxx Labs från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Fluxx Labs från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Fluxx Labs**i sökrutan och välj **Fluxx Labs** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Fluxx Labs i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Fluxx Labs baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Fluxx Labs upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Fluxx Labs måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Fluxx Labs Single Sign-On](#configure-fluxx-labs-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Fluxx Labs testanvändare](#create-fluxx-labs-test-user)** - att ha en motsvarighet till Britta Simon i Fluxx Labs som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Fluxx Labs:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Fluxx Labs-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Fluxx Labs Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Fluxx Labs Client supportteam](mailto:travis@fluxxlabs.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Fluxx Labs.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurera Fluxx Labs Enkel inloggning

1. Logga in på webbplatsen Fluxx Labs som administratör i ett annat webbläsarfönster.

2. Välj **Admin** under avsnittet **Inställningar.**

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config1.png)

3. På administratörspanelen väljer du > **Plugin-programintegreringar** och väljer sedan **SAML SSO-(Inaktiverad)** **Plug-ins**

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config2.png)

4. Gör följande i attributavsnittet:

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config3.png)

    a. Markera kryssrutan **SAML SSO.**

    b. Skriv **/auth/saml**i textrutan **Begär sökväg** .

    c. Skriv **/auth/saml/callback**i textrutan **Motringningssökväg** .

    d. I textrutan **Kontroll konsumenttjänst Url(Single Sign-On URL)** anger du värdet **Svara URL,** som du har angett i Azure-portalen.

    e. I textrutan **Målgrupp(SP Entitets-ID)** anger du **identifierarevärdet,** som du har angett i Azure-portalen.

    f. I textrutan **För identitetsprovider SSO-mål-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    g. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **identitetsprovidercertifikat.**

    h. Ange värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`i textrutan Format för **namnidentifierare.**

    i. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet har sparats visas fältet tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Fluxx Labs.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Fluxx Labs**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Fluxx Labs**i programlistan .

    ![Länken Fluxx Labs i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-fluxx-labs-test-user"></a>Skapa Fluxx Labs testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Fluxx Labs måste de etableras i Fluxx Labs. När det gäller Fluxx Labs är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen Fluxx Labs som administratör.

2. Klicka på nedanstående **ikon**.

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config6.png)

3. På instrumentpanelen klickar du på ikonen nedan för att öppna **kortet Nytt FOLK.**

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config4.png)

4. Gör följande i avsnittet **NYA PERSONER:**

    ![Fluxx Labs Konfiguration](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs använder e-post som unik identifierare för SSO-inloggningar. Fyll i **fältet SSO UID** med användarens e-postadress, som matchar e-postadressen, som de använder som inloggning med SSO.

    b. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Fluxx Labs på åtkomstpanelen ska du automatiskt loggas in på de Fluxx Labs som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

