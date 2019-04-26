---
title: 'Självstudier: Azure Active Directory-integrering med HubSpot | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adcd0f094d584e770f1a3f4938ee677ba58a21a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60275836"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudier: Azure Active Directory-integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (AD Azure).
Integrera HubSpot med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till HubSpot.
* Du kan aktivera användarna att vara automatiskt inloggad till HubSpot (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med HubSpot, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* HubSpot enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för HubSpot **SP och IDP** -initierad SSO

## <a name="adding-hubspot-from-the-gallery"></a>Att lägga till HubSpot från galleriet

För att konfigurera integrering av HubSpot i Azure AD, som du behöver lägga till HubSpot från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HubSpot från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **HubSpot**väljer **HubSpot** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![HubSpot i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HubSpot baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i HubSpot upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med HubSpot, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera HubSpot Single Sign-On](#configure-hubspot-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare HubSpot](#create-hubspot-test-user)**  – du har en motsvarighet för Britta Simon i HubSpot som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med HubSpot:

1. I den [Azure-portalen](https://portal.azure.com/)på den **HubSpot** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![HubSpot domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL som beskrivs senare i den här självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![HubSpot domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://app.hubspot.com/login`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera HubSpot** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hubspot-single-sign-on"></a>Konfigurera HubSpot Single Sign-On

1. Öppna en ny flik i webbläsaren och logga in på ditt administratörskonto för HubSpot.

2. Klicka på **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config1.png)

3. Klicka på **konto standardvärden**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config2.png)

4. Rulla ned till den **Security** och klicka på **konfigurera**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config3.png)

5. På den **Konfigurera enkel inloggning** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config4.png)

    a. Klickar du på **kopia** för att kopiera den **målgrupp URl(Service Provider Entity ID)** värde och klistra in den i den **identifierare** -textrutan i den **grundläggande SAML Konfigurationen** avsnitt i Azure-portalen.

    b. Klickar du på **kopia** för att kopiera den **logga på URL: en, ACS, mottagaren eller omdirigering** värde och klistra in den i den **svars-URL** -textrutan i den **grundläggande SAML Konfigurationen** avsnitt i Azure-portalen.

    c. I den **identitet identifierare eller utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.

    d. I den **enkel inloggnings-URL för identitetsprovider** textrutan klistra in den **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    e. Öppna din hämtade **Certificate(Base64)** -filen i anteckningar. Kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** box.

    f. Klicka på **Verifiera**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HubSpot.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **HubSpot**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **HubSpot**.

    ![Länken HubSpot i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hubspot-test-user"></a>Skapa HubSpot testanvändare

Om du vill aktivera Azure AD-användare att logga in på HubSpot, måste de etableras i HubSpot. När det gäller HubSpot är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **HubSpot** företagets plats som administratör.

2. Klicka på **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config1.png)

3. Klicka på **användare och team**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user1.png)

4. Klicka på **Skapa användare**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user2.png)

5. Ange e-postadress för användaren som `brittasimon\@contoso.com` i den **Lägg till e-addess(es)** textrutan och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user3.png)

6. På den **skapa användare** ,. Gå igenom varje enskild fliken och väljer lämpliga inställningar och behörigheter för användaren och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user4.png)

7. Klicka på **skicka** att skicka inbjudan till användaren.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren kommer aktiveras efter att acceptera inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen HubSpot i åtkomstpanelen, bör det vara loggas in automatiskt till HubSpot som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

