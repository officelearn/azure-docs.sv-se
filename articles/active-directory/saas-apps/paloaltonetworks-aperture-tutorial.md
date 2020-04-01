---
title: 'Självstudiekurs: Azure Active Directory-integrering med Palo Alto Networks - Aperture | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks - Aperture.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: fd498dc1c37ed6e9518fcefbdb237153504b5e98
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095068"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Självstudiekurs: Azure Active Directory-integrering med Palo Alto Networks - Bländare

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks - Aperture med Azure Active Directory (Azure AD).
Genom att integrera Palo Alto Networks – Aperture med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Palo Alto Networks - Aperture.
* Du kan aktivera dina användare automatiskt inloggade på Palo Alto Networks - Aperture (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Palo Alto Networks - Aperture behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - Aperture enda inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo Alto Networks - Aperture stöder **SP** och **IDP** initierade SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Lägga Palo Alto Networks - Bländare från galleriet

Om du vill konfigurera integreringen av Palo Alto Networks - Aperture i Azure AD måste du lägga till Palo Alto Networks - Aperture från galleriet till listan över hanterade SaaS-appar.

**Så här lägger du till Palo Alto Networks - Aperture från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Palo Alto Networks - Aperture**, väljer Palo **Alto Networks - Bländare** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

     ![Palo Alto Networks - Bländare i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Palo Alto Networks - Aperture baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Palo Alto Networks - Aperture upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Palo Alto Networks - Aperture måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Palo Alto Networks - Aperture Single Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Palo Alto Networks - Aperture testanvändare](#create-palo-alto-networks---aperture-test-user)** - att ha en motsvarighet till Britta Simon i Palo Alto Networks - Aperture som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Palo Alto Networks - Aperture:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Palo Alto Networks -** **Aperture-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Palo Alto Networks - Aperture Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Palo Alto Networks - Aperture Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Palo Alto Networks - Aperture Client supportteam](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Palo Alto-nätverk - Bländare.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurera Palo Alto-nätverk - Bländare enkel inloggning

1. I ett annat webbläsarfönster loggar du in på Palo Alto Networks - Aperture som administratör.

2. Klicka på **INSTÄLLNINGAR**på den övre menyraden .

    ![Fliken Inställningar](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigera till **programavsnittet** klicka på **Autentiseringsformulär** till vänster på menyn.

    ![Fliken Auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. På sidan **Autentisering** utför du följande steg:
    
    ![Fliken Autentisering](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Kontrollera **aktivera enkel inloggning (SSP-providers som stöds är Okta, En inloggning)** från fältet **Enkel inloggning.**

    b. Klistra in värdet för **Azure AD-identifierare**i textrutan **identitetsprovider-ID** som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** om du vill överföra det nedladdade certifikatet från Azure AD i fältet **Identitetsprovidercertifikat.**

    d. Klistra in värdet **för inloggnings-URL**i textrutan **För identitetsprovider SSO-URL** , som du har kopierat från Azure-portalen.

    e. Granska IdP-informationen från **Aperture Info-avsnittet** och hämta certifikatet från fältet **Bländarnyckel.**

    f. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto Networks - Aperture.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Palo Alto Networks - Aperture**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Palo Alto Networks - Aperture**.

    ![Palo Alto Networks - Aperture länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Skapa Palo Alto Networks - Aperture testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Palo Alto Networks - Aperture. Arbeta med [Palo Alto Networks - Aperture Client supportteam](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att lägga till användarna i Palo Alto Networks - Aperture-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Palo Alto Networks - Aperture i åtkomstpanelen bör du automatiskt loggas in på Palo Alto Networks - Aperture som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

