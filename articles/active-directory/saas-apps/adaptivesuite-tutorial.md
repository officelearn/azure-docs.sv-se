---
title: 'Självstudier: Azure Active Directory-integrering med anpassningsbar insikter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och anpassningsbar insikter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720157"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Självstudier: Azure Active Directory-integrering med anpassningsbar insikter

Lär dig hur du integrerar anpassningsbar insikter med Azure Active Directory (AD Azure) i den här självstudien.
Integrera anpassningsbar insikter med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har tillgång till anpassningsbara insikter.
* Du kan aktivera användarna att vara automatiskt inloggad till anpassningsbar Insights (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med anpassningsbar insikter, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Anpassningsbar insikter med enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Anpassningsbar Insights stöder **IDP** -initierad SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Lägga till anpassad Insights från galleriet

Om du vill konfigurera integreringen av anpassningsbar insikter om Azure AD, som du behöver lägga till anpassningsbar insikter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till anpassad insikter från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **anpassningsbar Insights**väljer **anpassningsbar Insights** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Anpassningsbar insikter i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med anpassningsbar insikter utifrån en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i anpassningsbar Insights upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med anpassningsbar Insights, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera anpassningsbar insikter enkel inloggning](#configure-adaptive-insights-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa anpassningsbar insikter testanvändare](#create-adaptive-insights-test-user)**  – du har en motsvarighet för Britta Simon i anpassningsbar insikter som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med anpassningsbar Insights:

1. I den [Azure-portalen](https://portal.azure.com/)på den **anpassningsbar Insights** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Anpassningsbar insikter domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Du kan hämta identifierare (entitets-ID) och svars-URL-värden från de anpassningsbara insikter **SAML SSO-inställningar** sidan.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **ställa in anpassningsbar insikter** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-adaptive-insights-single-sign-on"></a>Konfigurera anpassningsbar insikter enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din anpassningsbar insikter företagets webbplats som administratör.

2. Gå till **Admin**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. I den **användare och roller** klickar du på **hantera inställningar för SAML SSO**.

    ![Hantera inställningar för enkel inloggning SAML](./media/adaptivesuite-tutorial/ic805645.png "hantera SAML SSO-inställningar")

4. På den **SAML SSO-inställningar** utför följande steg:

    ![Inställningar för enkel inloggning SAML](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-inställningar")

    a. I den **namn på identitetsprovider** textrutan anger du ett namn för din konfiguration.

    b. Klistra in den **Azure Ad-identifierare** värdet som har kopierats från Azure-portalen till den **identitetsprovider entitets-ID** textrutan.

    c. Klistra in den **inloggnings-URL** värdet som har kopierats från Azure-portalen till den **identitetsprovider SSO URL** textrutan.

    d. Klistra in den **URL för utloggning** värdet som har kopierats från Azure-portalen till den **anpassad URL för utloggning** textrutan.

    e. Om du vill ladda upp din hämtade certifikatet klickar du på **Välj fil**.

    f. Välj följande, för:

     * **Användar-id för SAML**väljer **användarens anpassningsbar information användarnamn**.

     * **SAML-id Användarplats**väljer **användar-id i NameID ämne**.

     * **SAML NameID-format**väljer **e-postadress**.

     * **Aktivera SAML**väljer **Tillåt SAML SSO och anpassningsbar Insights direktinloggning**.

    g. Kopiera **anpassningsbar Insights SSO-URL** och klistra in i den **identifierare (entitets-ID)** och **svars-URL** textrutor i den **anpassningsbar insikter domän och URL: er** avsnitt i Azure-portalen.

    h. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp 'brittasimon@yourcompanydomain.extension. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till anpassningsbar insikter.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **anpassningsbar insikter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **anpassningsbar insikter**.

    ![Länken anpassningsbar insikter i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adaptive-insights-test-user"></a>Skapa anpassningsbar insikter testanvändare

Om du vill aktivera Azure AD-användare att logga in på anpassningsbar insikter, måste de etableras till anpassningsbar insikter. När det gäller anpassningsbar insikter är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **anpassningsbar insikter** företagets plats som administratör.

2. Gå till **Admin**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. I den **användare och roller** klickar du på **Lägg till användare**.

   ![Lägg till användare](./media/adaptivesuite-tutorial/IC805648.png "Lägg till användare")

4. I den **ny användare** avsnittet, utför följande steg:

   ![Skicka](./media/adaptivesuite-tutorial/IC805649.png "skicka")

   a. Typen i **namn**, **inloggning**, **e-post**, **lösenord** av en giltig Azure Active Directory-användare du vill etablera i den relaterade textrutor.

   b. Välj en **rollen**.

   c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda anpassningsbar insikter användaren-konto skapas verktyg från någon annan eller API: er som tillhandahålls av anpassningsbar insikter att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen anpassningsbar insikter i åtkomstpanelen, bör det vara loggas in automatiskt till de anpassningsbara insikter som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)