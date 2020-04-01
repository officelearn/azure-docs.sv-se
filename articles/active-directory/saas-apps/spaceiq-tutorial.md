---
title: 'Självstudiekurs: Azure Active Directory-integrering med SpaceIQ | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090095"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Självstudiekurs: Azure Active Directory-integrering med SpaceIQ

I den här självstudien får du lära dig hur du integrerar SpaceIQ med Azure Active Directory (Azure AD).
Genom att integrera SpaceIQ med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpaceIQ.
* Du kan aktivera dina användare så att de automatiskt loggas in på SpaceIQ (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SpaceIQ behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SpaceIQ enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SpaceIQ stöder **IDP** initierad SSO

## <a name="adding-spaceiq-from-the-gallery"></a>Lägga till SpaceIQ från galleriet

Om du vill konfigurera integreringen av SpaceIQ i Azure AD måste du lägga till SpaceIQ från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SpaceIQ från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SpaceIQ**i sökrutan och välj **SpaceIQ** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![SpaceIQ i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med SpaceIQ baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SpaceIQ upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SpaceIQ måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpaceIQ Single Sign-On](#configure-spaceiq-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SpaceIQ-testanvändare](#create-spaceiq-test-user)** – om du vill ha en motsvarighet till Britta Simon i SpaceIQ som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SpaceIQ:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SpaceIQ-programintegration**. **SpaceIQ**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![SpaceIQ-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv URL:en i textrutan **Identifierare:**`https://api.spaceiq.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Uppdatera dessa värden med den faktiska svars-URL:en och identifieraren som förklaras senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SpaceIQ.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-spaceiq-single-sign-on"></a>Konfigurera SpaceIQ enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga sedan in på SpaceIQ-miljön som administratör.

1. När du är inloggad, klicka på pussel tecken längst upp till höger, klicka sedan på **Integrationer**

    ![Kontoinställningar](./media/spaceiq-tutorial/setting1.png) 

1. Under **All ETABLERING & SSO**klickar du på **Azure-panelen** för att lägga till en instans av Azure som IDP.

    ![Ikon för SAML](./media/spaceiq-tutorial/setting2.png)

1. Gör följande i dialogrutan **SSO:**

    ![Inställningar för SAML-autentisering](./media/spaceiq-tutorial/setting3.png)

    a. I **url-rutan för SAML-utfärdare** klistrar du in azure **AD-identifierare** som kopierats från konfigurationsfönstret för Azure AD-program.

    b. Kopiera **URL-url:en för SAML-motringningsslutpunkt (skrivskyddad)** och klistra in värdet i rutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. Kopiera **SAML Audience URI-värdet (skrivskyddad)** och klistra in värdet i rutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    d. Öppna den nedladdade certifikatfilen i anteckningar, kopiera innehållet och klistra sedan in den i rutan **X.509 Certificate.**

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
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpaceIQ.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SpaceIQ**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SpaceIQ**i programlistan .

    ![Länken SpaceIQ i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-spaceiq-test-user"></a>Skapa SpaceIQ-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i SpaceIQ. Work [SpaceIQ supportteam](mailto:eng@spaceiq.com) för att lägga till användarna i SpaceIQ-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på spaceIQ-panelen på åtkomstpanelen ska du automatiskt loggas in på den SpaceIQ som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

