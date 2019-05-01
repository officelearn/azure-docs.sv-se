---
title: 'Självstudier: Azure Active Directory-integrering med Replicon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be006cd49041a33c13b5cc5c459a1d54c46c3b47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727601"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Självstudier: Azure Active Directory-integrering med Replicon

I den här självstudien får du lära dig hur du integrerar Replicon med Azure Active Directory (AD Azure).
Integrera Replicon med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Replicon.
* Du kan aktivera användarna att vara automatiskt inloggad till Replicon (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Replicon, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Replicon enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för replicon **SP** -initierad SSO

## <a name="adding-replicon-from-the-gallery"></a>Att lägga till Replicon från galleriet

För att konfigurera integrering av Replicon i Azure AD, som du behöver lägga till Replicon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Replicon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Replicon**väljer **Replicon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Replicon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Replicon baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Replicon upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Replicon, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Replicon Single Sign-On](#configure-replicon-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Replicon](#create-replicon-test-user)**  – du har en motsvarighet för Britta Simon i Replicon som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Replicon:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Replicon** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Replicon domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://global.replicon.com/<companyname>`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Replicon klienten supportteamet](https://www.replicon.com/customerzone/contact-support) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Replicon** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-replicon-single-sign-on"></a>Konfigurera Replicon enkel inloggning

1. Logga in på webbplatsen Replicon företag som en administratör i ett annat webbläsarfönster.

2. Utför följande steg för att konfigurera SAML 2.0:

    ![Aktivera SAML-autentisering](./media/replicon-tutorial/ic777805.png "aktivera SAML-autentisering")

    a. Att visa den **EnableSAML Authentication2** dialogrutan lägger du till följande till din URL efter företagets nyckeln: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Nedan visas schemat för den fullständiga URL: en: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicka på den **+** att expandera den **v20Configuration** avsnittet.

   c. Klicka på den **+** att expandera den **metaDataConfiguration** avsnittet.

   d. Klicka på **Välj fil**, för att välja din identitet providern XML-fil och klicka på **skicka**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Replicon.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Replicon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Replicon**.

    ![Länken Replicon i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-replicon-test-user"></a>Skapa Replicon testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Replicon.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen Replicon företag som en administratör i ett webbläsarfönster.

2. Gå till **Administration \> användare**.

    ![Användare](./media/replicon-tutorial/ic777806.png "Användare")

3. Klicka på **+ Lägg till användare**.

    ![Lägg till användare](./media/replicon-tutorial/ic777807.png "Lägg till användare")

4. I den **användarprofil** avsnittet, utför följande steg:

    ![Användarprofil](./media/replicon-tutorial/ic777808.png "användarprofil")

    a. I den **inloggningsnamn** textrutan Ange Azure AD-e-postadress för Azure AD-användare som du vill etablera som `BrittaSimon\@contoso.com`.

    b. Som **autentiseringstyp**väljer **SSO**.

    c. I den **avdelning** textrutan skriver användarens avdelning.

    d. Som **typ av anställd**väljer **administratör**.

    e. Klicka på **spara användarprofil**.

> [!NOTE]
> Du kan använda alla andra Replicon användare konto verktyg för att skapa eller API: er som tillhandahålls av Replicon att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Replicon i åtkomstpanelen, bör det vara loggas in automatiskt till Replicon som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)