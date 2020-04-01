---
title: 'Självstudiekurs: Azure Active Directory-integrering med xMatters OnDemand | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086503"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudiekurs: Azure Active Directory-integrering med xMatters OnDemand

I den här självstudien får du lära dig hur du integrerar xMatters OnDemand med Azure Active Directory (Azure AD).
Genom att integrera xMatters OnDemand med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till xMatters OnDemand.
* Du kan aktivera dina användare så att de automatiskt loggas in på xMatters OnDemand (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med xMatters OnDemand behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* xMatters OnDemand enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* xMatters OnDemand stöder **IDP** initierad SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Lägga till xMatters OnDemand från galleriet

Om du vill konfigurera integreringen av xMatters OnDemand i Azure AD måste du lägga till xMatters OnDemand från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till xMatters OnDemand från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **xMatters OnDemand**i sökrutan och välj **xMatters OnDemand** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![xMatters OnDemand i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med xMatters OnDemand baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i xMatters OnDemand upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med xMatters OnDemand måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera xMatters OnDemand Enkel inloggning](#configure-xmatters-ondemand-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa xMatters OnDemand-testanvändare](#create-xmatters-ondemand-test-user)** – om du vill ha en motsvarighet till Britta Simon i xMatters OnDemand som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med xMatters OnDemand:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **xMatters OnDemand-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![xMatters OnDemand Domän och WEBBADRESSER enkel inloggningsinformation](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [xMatters OnDemand Client supportteam](https://www.xmatters.com/company/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!IMPORTANT]
    > Du måste vidarebefordra certifikatet till [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/). Certifikatet måste överföras av xMatters supportteam innan du kan slutföra den enda inloggningskonfigurationen.

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera xMatters OnDemand.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurera xMatters OnDemand Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din XMatters OnDemand-företagswebbplats som administratör.

2. Klicka på **Admin**i verktygsfältet högst upp och klicka sedan på **Företagsinformation** i navigeringsfältet till vänster.

    ![Administratör](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

3. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![SAML-konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfiguration")

    a. Välj **Aktivera SAML**.

    b. I textrutan **Identitetsprovider-ID** klistrar du in **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I textrutan **Enkel inloggning av URL** klistrar du in **url-värde** för inloggning som du har kopierat från Azure-portalen.

    d. I textrutan **URL för enkel utloggning** klistrar du in **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Klicka på **Spara ändringar**högst upp på sidan Företagsinformation.

    ![Företagsuppgifter](./media/xmatters-ondemand-tutorial/IC776797.png "Företagsuppgifter")

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till xMatters OnDemand.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **xMatters OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **xMatters OnDemand**i programlistan .

    ![Länken xMatters OnDemand i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-xmatters-ondemand-test-user"></a>Skapa xMatters OnDemand-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i xMatters OnDemand.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **XMatters OnDemand-klient.**

2. Klicka på fliken **Användare** och sedan på **Lägg till användare**.

    ![Användare](./media/xmatters-ondemand-tutorial/IC781048.png "Användare")

3. Gör följande i avsnittet **Lägg till användare:**

    ![Lägga till en användare](./media/xmatters-ondemand-tutorial/IC781049.png "Lägga till en användare")

    a. Välj **Active** (Aktiv).

    b. Skriv **användar-ID:t** för användaren som Brittasimon@contoso.com.

    c. I textrutan för **förnamn** skriver du förnamnet på användaren, till exempel Britta.

    d. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    e. Ange den giltiga platsen för ett giltigt Azure AD-konto som du vill etablera i textrutan **Webbplats.**

    f. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen xMatters OnDemand på åtkomstpanelen ska du automatiskt loggas in på den xMatters OnDemand som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

