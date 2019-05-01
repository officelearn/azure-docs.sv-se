---
title: 'Självstudier: Azure Active Directory-integrering med KnowledgeOwl | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: b57beb18b8bf7d82694f89e131d79d15c6fcd6bc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728291"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Självstudier: Azure Active Directory-integrering med KnowledgeOwl

I den här självstudien får du lära dig hur du integrerar KnowledgeOwl med Azure Active Directory (AD Azure).
Integrera KnowledgeOwl med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till KnowledgeOwl.
* Du kan aktivera användarna att vara automatiskt inloggad till KnowledgeOwl (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med KnowledgeOwl, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* KnowledgeOwl enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för KnowledgeOwl **SP och IDP** -initierad SSO
* Har stöd för KnowledgeOwl **Just In Time** etableringen av användare

## <a name="adding-knowledgeowl-from-the-gallery"></a>Att lägga till KnowledgeOwl från galleriet

För att konfigurera integrering av KnowledgeOwl i Azure AD, som du behöver lägga till KnowledgeOwl från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till KnowledgeOwl från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **KnowledgeOwl**väljer **KnowledgeOwl** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![KnowledgeOwl i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i KnowledgeOwl upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera KnowledgeOwl Single Sign-On](#configure-knowledgeowl-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare KnowledgeOwl](#create-knowledgeowl-test-user)**  – du har en motsvarighet för Britta Simon i KnowledgeOwl som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med KnowledgeOwl:

1. I den [Azure-portalen](https://portal.azure.com/)på den **KnowledgeOwl** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![KnowledgeOwl domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:

    ||
    |-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![KnowledgeOwl domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värdet från faktiska identifierare svars-URL och inloggnings-URL som beskrivs senare i självstudien.

6. Programmets KnowledgeOwl förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på **redigera** ikonen för att öppna **användarattribut** dialogrutan.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut | Namnområde |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I listan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

9. På den **konfigurera KnowledgeOwl** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-knowledgeowl-single-sign-on"></a>Konfigurera KnowledgeOwl Single Sign-On

1. Logga in på webbplatsen KnowledgeOwl företag som en administratör i ett annat webbläsarfönster.

1. Klicka på **inställningar** och välj sedan **Security**.

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure1.png)

1. Bläddra till **SAML SSO-integrering** och utför följande steg:

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure2.png)

    a. Välj **aktivera SAML SSO**.

    b. Kopiera den **SP entitets-ID** värde och klistra in den i den **identifierare (entitets-ID)** i den **SAML grundkonfiguration** avsnittet på Azure portal.

    c. Kopiera den **SP inloggnings-URL** värde och klistra in den i den **inloggnings-URL och svars-URL** textrutor i den **SAML grundkonfiguration** avsnittet på Azure portal.

    d. I den **IdP entityID** textrutan klistra in den **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    e. I den **inloggnings-URL för IDP: N** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    f. I den **utloggnings-URL för IDP: N** textrutan klistra in den **URL för utloggning** värde, som du har kopierat från Azure portal

    g. Ladda upp nedladdade certifikatet från Azure portal genom att klicka på den **överför certifikat för IDP: N**.

    h. Klicka på **kartan SAML-attribut** att mappa attribut och utför följande steg:

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure3.png)

    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` till den **SSO-ID** textrutan
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` till den **användarnamn/e-post** textrutan.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` till den **Förnamn** textrutan.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` till den **efternamn** textrutan.
    * Klicka på **Spara**

    i. Klicka på **Spara** längst ned på sidan.

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till KnowledgeOwl.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **KnowledgeOwl**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **KnowledgeOwl**.

    ![Länken KnowledgeOwl i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-knowledgeowl-test-user"></a>Skapa KnowledgeOwl testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i KnowledgeOwl. KnowledgeOwl stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i KnowledgeOwl, skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [KnowledgeOwl supportteamet](mailto:support@knowledgeowl.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen KnowledgeOwl i åtkomstpanelen, bör det vara loggas in automatiskt till KnowledgeOwl som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)