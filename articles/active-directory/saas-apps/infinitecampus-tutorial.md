---
title: 'Självstudiekurs: Azure Active Directory-integrering med Infinite Campus | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100357"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Självstudiekurs: Azure Active Directory-integrering med Infinite Campus

I den här självstudien får du lära dig hur du integrerar Infinite Campus med Azure Active Directory (Azure AD).
Genom att integrera Infinite Campus med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Infinite Campus.
* Du kan aktivera dina användare automatiskt inloggade på Infinite Campus (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Infinite Campus behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Oändlig Campus enkel inloggning aktiverad prenumeration
* Åtminstone måste du vara en Azure Active Directory-administratör och ha en campusproduktsäkerhetsroll för "Student Information System (SIS)" för att slutföra konfigurationen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Infinite Campus stöder **SP** initierade SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Lägga till Infinite Campus från galleriet

Om du vill konfigurera integreringen av Infinite Campus i Azure AD måste du lägga till Infinite Campus från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Infinite Campus från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Infinite Campus**i sökrutan och välj **Oändligt campus** på resultatpanelen och klicka sedan på knappen **Lägg** till för att lägga till programmet.

    ![Oändligt universitetsområde i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Infinite Campus baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Infinite Campus upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Infinite Campus måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Infinite Campus Single Sign-On](#configure-infinite-campus-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa infinite campus testanvändare](#create-infinite-campus-test-user)** - att ha en motsvarighet till Britta Simon i Infinite Campus som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med Infinite Campus:

1. Välj Enkel inloggning på sidan Infinite Campus-programintegration på **Infinite Campus** [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet Grundläggande SAML-konfiguration utför du följande steg (observera att domänen kommer att variera med värdmodellen, men värdet fullständigt kvalificerad domän måste matcha din Infinite **Campus-installation):**

    a. Skriv en URL i textrutan **Sign-on URL** med följande mönster:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Skriv en URL i textrutan **Identifierare** med följande mönster:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Oändlig campusdomän och webbadresser med enkel inloggning](common/sp-identifier-reply.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurera oändlig campus enkel inloggning

1. Logga in på Infinite Campus som säkerhetsadministratör i ett annat webbläsarfönster.

2. Klicka på **Systemadministration**till vänster på menyn .

    ![Administratören](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigera till konfigurationen av**SAML-hantering** > **av** **ANVÄNDARSÄKERHET** > .

    ![Den saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Gör följande på sidan Konfiguration av **SSO-tjänstprovider:**

    ![Den sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Välj **Aktivera SAML Enkel inloggning**.

    b. Redigera det **valfria attributnamnet** så att det innehåller **namn**

    c. I avsnittet **Välj ett alternativ för att hämta IDP-serverdata (Identity Provider)** väljer du Url för **metadata,** klistrar in **url-värdet för appfederationens metadata,** som du har kopierat från Azure-portalen i rutan och klickar sedan på **Synkronisera**.

    d. När du har klickat **på Synkronisera** värdena fylls de i automatiskt på konfigurationssidan för **SSO-tjänstprovider.** Dessa värden kan verifieras för att matcha de värden som visas i steg 4 ovan.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

> [!NOTE]
> Om du vill att alla Dina Azure-användare ska ha enkel inloggningsåtkomst till Infinite Campus och förlita dig på infinite campus-systemet för intern behörighet för att styra åtkomsten, kan du ange egenskapen **Användartilldelning krävs** för programmet till Nej och hoppa över följande steg.

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Infinite Campus.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Infinite **Campus**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Oändligt campus**i programlistan .

    ![Länken Oändligt campus i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-infinite-campus-test-user"></a>Skapa en oändlig testanvändare för Campus

Infinite Campus har en demografi centrerad arkitektur. Kontakta [Infinite Campus supportteam](mailto:sales@infinitecampus.com) för att lägga till användarna i Infinite Campus-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Oändligt campus på åtkomstpanelen ska du automatiskt loggas in på det oändliga campus som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
