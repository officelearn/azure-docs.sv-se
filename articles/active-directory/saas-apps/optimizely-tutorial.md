---
title: 'Självstudiekurs: Azure Active Directory-integrering med Optimizely | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943947"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Självstudiekurs: Azure Active Directory-integrering med Optimizely

I den här självstudien får du lära dig hur du integrerar Optimerat med Azure Active Directory (Azure AD).
Genom att integrera optimerat med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Optimizely.
* Du kan aktivera dina användare så att de automatiskt loggas in på Optimizely (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Optimizely behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Optimerad enkel inloggningsaktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Optimerat stöder **SP** initierade SSO

## <a name="adding-optimizely-from-the-gallery"></a>Lägga till optimerat från galleriet

Om du vill konfigurera integreringen av Optimizely i Azure AD måste du lägga till Optimerat från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Optimerat från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Optimerat**i sökrutan och välj **Optimerat** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Optimerat i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Optimizely baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Optimizely upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Optimizely måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Optimerat enkel inloggning](#configure-optimizely-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Optimerat testanvändare](#create-optimizely-test-user)** - att ha en motsvarighet till Britta Simon i Optimizely som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Optimizely:

1. Välj Enkel inloggning på sidan **Optimerat** programintegrering på [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Optimerad domän- och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://app.optimizely.net/<instance name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > De här värdena är inte verkliga. Du kommer att uppdatera värdet med den faktiska inloggnings-URL:en och identifieraren, som förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ditt Optimizely-program förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon om du vill öppna dialogrutan **Användarattribut.**

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig Optimizely-programmet att få fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut |
    | ---------------| --------------- |
    | e-post | user.mail |
    
    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera optimerat.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-optimizely-single-sign-on"></a>Konfigurera optimerat enkel inloggning

1. Om du vill konfigurera enkel inloggning på **optimerad** sida kontaktar du din Optimizely Account Manager och tillhandahåller det nedladdade **certifikatet (Base64)** och lämpliga kopierade webbadresser.

2. Som svar på din e-post ger Optimizely dig värdena Sign On-URL (SP-initierad SSO) och identifieraren (Service Provider Entity ID).

    a. Kopiera den **SP-initierade SSO-URL:en** som tillhandahålls av Optimizely och klistra in i textrutan Logga in **på URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    b. Kopiera **tjänstprovidernsentitets-ID** som tillhandahålls av Optimizely och klistra in i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

3. I ett annat webbläsarfönster loggar du in på ditt Optimizely-program.

4. Klicka på kontonamnet längst upp till höger och sedan **kontoinställningar**.

    ![Enkel azure AD-inloggning](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Markera kryssrutan **Aktivera SSO** under Enkel inloggning i avsnittet **Översikt** på fliken Konto.
  
    ![Enkel azure AD-inloggning](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klicka på **Spara**

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Optimizely.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Optimera **.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Optimerat**i programlistan .

    ![Länken Optimerat i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-optimizely-test-user"></a>Skapa optimerat testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Optimizely.

1. Välj fliken **Medarbetare** på startsidan.

2. Om du vill lägga till nya medarbetare i projektet klickar du på **Ny medarbetare**.
   
    ![Skapa en testanvändare för Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Fyll i e-postadressen och tilldela dem en roll. Klicka på **Bjud in**.

    ![Skapa en testanvändare för Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. De får en e-post inbjudan. Med hjälp av e-postadressen måste de logga in på Optimizely.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Optimerat på åtkomstpanelen bör du automatiskt loggas in på den optimerade för vilken du konfigurerar SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

