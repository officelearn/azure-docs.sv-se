---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAML SSO for Bamboo by resolution GmbH | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Bambu genom resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106541"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Självstudiekurs: Azure Active Directory-integrering med SAML SSO for Bamboo by resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO for Bamboo by resolution GmbH med Azure Active Directory (Azure AD).
Genom att integrera SAML SSO for Bamboo by resolution GmbH med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SAML SSO för Bambu genom resolution GmbH.
* Du kan aktivera dina användare automatiskt inloggad på SAML SSO for Bamboo by resolution GmbH (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SAML SSO for Bamboo by resolution GmbH behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SAML SSO för Bamboo genom resolution GmbH enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO for Bamboo by resolution GmbH stöder **SP och IDP** initierade SSO
* SAML SSO för Bamboo by resolution GmbH stöder **Just In Time** användaretablering

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Lägga till SAML SSO för Bamboo genom resolution GmbH från galleriet

Om du vill konfigurera integreringen av SAML SSO for Bamboo by resolution GmbH i Azure AD måste du lägga till SAML SSO för Bamboo by resolution GmbH från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SAML SSO for Bamboo by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAML SSO for Bamboo by resolution GmbH**, väljer **SAML SSO för Bamboo by resolution GmbH** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

    ![SAML SSO för Bamboo av resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med SAML SSO for Bamboo by resolution GmbH baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Bamboo by resolution GmbH upprättas.

Om du vill konfigurera och testa en azure AD-inloggning med SAML SSO for Bamboo by resolution GmbH måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SAML SSO för Bambu genom upplösning GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på applikationssidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAML SSO för Bambu genom resolution GmbH testanvändare](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** - att ha en motsvarighet till Britta Simon i SAML SSO för Bambu genom resolution GmbH som är kopplad till Azure AD representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SAML SSO for Bamboo by resolution GmbH:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på **sidan SAML SSO for Bamboo by resolution GmbH-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![SAML SSO för Bambu genom resolution GmbH Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<server-base-url>/plugins/servlet/samlsso`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SAML SSO för Bambu genom resolution GmbH Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SAML SSO för Bamboo genom resolution GmbH Client supportteam](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SAML SSO for Bamboo by resolution GmbH.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Konfigurera SAML SSO för Bambu genom upplösning GmbH Single Sign-On

1. Logga in på din SAML SSO for Bamboo genom resolution GmbH företagets webbplats som administratör.

1. Klicka på **Inställningar** > till höger i huvudverktygsfältet.**Add-ons**

    ![Inställningarna](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Gå till SECURITY avsnitt, klicka på **SAML SingleSignOn** på menyraden.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. På **konfigurationssidan för SAML SIngleSignOn-plugin-programmet** klickar du på **Lägg till IdP**.

    ![Lägg till IdP](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. På sidan **Välj din SAML-identitetsprovider** utför du följande steg:

    ![Identitetsprovidern](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Välj **IdP-typ** as **AZURE AD**.

    b. I textrutan **Namn** skriver du namnet.

    c. I textrutan **Beskrivning** skriver du beskrivningen.

    d. Klicka på **Nästa**.

1. Klicka på **Nästa**på **konfigurationssidan för identitetsprovidern** .

    ![Identitetskonfigurationen](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. På sidan **Importera SAML IdP-metadata** klickar du på **Läs in fil** för att ladda upp den **METADATA XML**-fil som du har laddat ned från Azure-portalen.

    ![IdP-metadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klicka på **Nästa**.

1. Klicka **Spara inställningar**.

    ![Spara](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till SAML SSO för Bamboo by resolution GmbH.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SAML SSO för Bambu efter upplösning GmbH**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SAML SSO för Bamboo by resolution GmbH**.

    ![SAML SSO för Bambu genom resolution GmbH länk i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Skapa SAML SSO för Bamboo by resolution GmbH testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i SAML SSO för Bamboo av resolution GmbH. SAML SSO for Bamboo by resolution GmbH stöder just-in-time-etablering och även användare kan skapas manuellt, kontakta [SAML SSO för Bamboo genom resolution GmbH Client support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) enligt dina krav.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på PANELEN SAML SSO for Bamboo by resolution GmbH i åtkomstpanelen bör du automatiskt loggas in på SAML SSO for Bamboo by resolution GmbH som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
