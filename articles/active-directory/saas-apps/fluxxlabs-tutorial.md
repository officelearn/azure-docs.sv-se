---
title: 'Självstudier: Azure Active Directory-integrering med Fluxx Labs | Microsoft Docs'
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
ms.openlocfilehash: 2a2f8fb9cd84e9177ec351eae986a87c184f8f00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543618"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Självstudier: Azure Active Directory-integrering med Fluxx Labs

I den här självstudien får du lära dig hur du integrerar Fluxx Labs med Azure Active Directory (AD Azure).
Integrera Fluxx Labs med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Fluxx Labs.
* Du kan aktivera användarna att vara automatiskt inloggad till Fluxx övningar (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Fluxx Labs, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Fluxx Labs enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Fluxx Labs **IDP** -initierad SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Att lägga till Fluxx Labs från galleriet

För att konfigurera integrering av Fluxx labb i Azure AD, som du behöver lägga till Fluxx Labs från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fluxx Labs från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Fluxx Labs**väljer **Fluxx Labs** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Fluxx Labs i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Fluxx Labs baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Fluxx Labs upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fluxx Labs, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Fluxx-Labs](#configure-fluxx-labs-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Fluxx Labs testanvändare](#create-fluxx-labs-test-user)**  – du har en motsvarighet för Britta Simon i Fluxx Labs som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Fluxx Labs:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Fluxx Labs** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Fluxx Labs domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

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
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Fluxx Labs-klienten supportteamet](mailto:travis@fluxxlabs.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Fluxx labb** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurera enkel inloggning för Fluxx-labb

1. I ett annat webbläsarfönster, loggar du in din Fluxx Labs företagets webbplats som administratör.

2. Välj **Admin** under den **inställningar** avsnittet.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config1.png)

3. I panelen administratören väljer **plugins** > **integreringar** och välj sedan **SAML SSO-(Disabled)**

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config2.png)

4. Utför följande steg i avsnittet attribut:

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config3.png)

    a. Välj den **SAML SSO** kryssrutan.

    b. I den **begär sökväg** textrutan typ **/auth/saml**.

    c. I den **återanrop sökväg** textrutan typ **/auth/saml/callback**.

    d. I den **Assertion konsument Service Url(Single Sign-On URL)** textrutan anger du den **svars-URL** värde, som du har angett i Azure-portalen.

    e. I den **målgrupp (SP entitets-ID)** textrutan anger du den **identifierare** värde, som du har angett i Azure-portalen.

    f. I den **SSO mål-URL för identitetsprovider** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    g. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **Identity Provider Certificate** (Certifikat för identitetsprovider).

    h. I **namnidentifierare Format** textrutan anger du värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet sparas, fältet visas tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fluxx Labs.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Fluxx Labs**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Fluxx Labs**.

    ![Länken Fluxx Labs i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-fluxx-labs-test-user"></a>Skapa Fluxx Labs testanvändare

Om du vill aktivera Azure AD-användare att logga in på Fluxx Labs, måste de etableras i Fluxx Labs. När det gäller Fluxx Labs är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Fluxx Labs företagets som administratör.

2. Klicka på den nedan visas **ikonen**.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config6.png)

3. På instrumentpanelen, klickar du på den nedan visas ikonen för att öppna den **nya personer** kort.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config4.png)

4. På den **nya personer** avsnittet, utför följande steg:

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs använda e-post som den unika identifieraren för SSO-inloggningar. Fyll i den **SSO UID** med användarens e-postadress som matchar den e-postadress som de använder som inloggning med enkel inloggning.

    b. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Fluxx Labs i åtkomstpanelen, bör det vara loggas in automatiskt till Fluxx-labb som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

