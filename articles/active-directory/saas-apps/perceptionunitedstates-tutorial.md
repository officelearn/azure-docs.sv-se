---
title: 'Självstudiekurs: Azure Active Directory-integrering med Perception United States (non-UltiPro) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Perception United States (non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094841"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Självstudiekurs: Azure Active Directory-integrering med Perception United States (icke-UltiPro)

I den här självstudien får du lära dig hur du integrerar Perception United States (non-UltiPro) med Azure Active Directory (Azure AD).
Genom att integrera Perception United States (non-UltiPro) med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Perception UNITED States (Non-UltiPro).
* Du kan aktivera dina användare automatiskt inloggade på Perception USA (non-UltiPro) (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Perception United States (icke-UltiPro) behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Perception United States (Non-UltiPro) enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Perception UNITED States (Non-UltiPro) stöder **IDP** initierad SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Lägga Perception USA (Icke-UltiPro) från galleriet

Om du vill konfigurera integreringen av Perception United States (Non-UltiPro) i Azure AD måste du lägga till Perception United States (Non-UltiPro) från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Perception United States (Non-UltiPro) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Perception USA (icke-UltiPro)** och väljer **Perception USA (icke-UltiPro)** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

     ![Perception Usa (Icke-UltiPro) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Perception United States (Non-UltiPro) baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Perception UNITED States (Non-UltiPro) upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Perception United States (icke-UltiPro) måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Perception United States (Non-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Perception United States (Non-UltiPro) testanvändare](#create-perception-united-states-non-ultipro-test-user)** - att ha en motsvarighet till Britta Simon i Perception USA (Icke-UltiPro) som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Perception United States (icke-UltiPro) gör du följande:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan Perception United **States (Non-UltiPro)** programintegration .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Perception Usa (icke-UltiPro) Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL i textrutan **Identifierare:**`https://perception.kanjoya.com/sp`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. **Perception-PROGRAMMET (icke-UltiPro)** kräver **azure AD-identifierare** som <entity_id>, som du får från avsnittet **Konfigurera Perception USA (non-UltiPro)** som ska kodas. Använd följande länk för att få det uri-kodade värdet: **http://www.url-encode-decode.com/**.

    d. Efter att ha fått uri kodade värdet kombinera det med **svars-URL** som nämns nedan-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Klistra in ovanstående värde i textrutan **Svara på URL.**

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera perception i USA (icke-UltiPro)** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurera perceptions-enigt usa (icke-UltiPro) enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din webbplats för Perception United States (Non-UltiPro) som administratör.

2. Klicka på **Kontoinställningar**i huvudverktygsfältet .

    ![Perception United States (Icke-UltiPro) användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Gör följande på sidan **Kontoinställningar:**

    ![Perception United States (Icke-UltiPro) användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Skriv namnet på **företaget**i textrutan **Företagsnamn** .
    
    b. Skriv namnet på **kontot**i textrutan **Kontonamn** .

    c. I textrutan **Standardsvar-till-e-post** skriver du den giltiga **e-postadressen**.

    d. Välj **SSO-identitetsprovider** som **SAML 2.0**.

4. Gör följande på sidan **SSO-konfiguration:**

    ![Perception USA (Icke-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Välj **SAML-namnidtyp** som **e-post**.

    b. Skriv namnet på **konfigurationen**i textrutan **SSO-konfigurationsnamn** .
    
    c. Klistra in värdet för **Azure AD-identifierare**i textrutan **Identitetsprovidernamn** som du har kopierat från Azure-portalen. 

    d. I **SMS:ar för SAML-domän**anger du domänen som @contoso.com.

    e. Klicka på **Ladda upp igen** för att ladda upp metadata **XML-filen.**

    f. Klicka på **Uppdatera**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Perception UNITED States (Non-UltiPro).

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Perception USA **(icke-UltiPro).**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj Perception USA **(Icke-UltiPro)** i programlistan .

    ![Länken Perception United States (Non-UltiPro) i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Skapa perception usa (icke-UltiPro) testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Perception UNITED States (Non-UltiPro). Arbeta med [Perception United States (Non-UltiPro) supportteam](https://www.ultimatesoftware.com/Contact/ContactUs) för att lägga till användare i Perception UNITED States (Non-UltiPro) plattform.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Perception USA (icke-UltiPro) på åtkomstpanelen bör du automatiskt loggas in på perceptions-USA (icke-UltiPro) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

