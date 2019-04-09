---
title: 'Självstudier: Azure Active Directory-integrering med Optimizely | Microsoft Docs'
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
ms.openlocfilehash: 066847ff8428a5080e655712c1a6bbe504c5cdbc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257798"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Självstudier: Azure Active Directory-integrering med Optimizely

I den här självstudien får du lära dig hur du integrerar Optimizely med Azure Active Directory (AD Azure).
Integrera Optimizely med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Optimizely.
* Du kan aktivera användarna att vara automatiskt inloggad till Optimizely (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Optimizely, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Optimizely enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Optimizely **SP** -initierad SSO

## <a name="adding-optimizely-from-the-gallery"></a>Att lägga till Optimizely från galleriet

För att konfigurera integrering av Optimizely i Azure AD, som du behöver lägga till Optimizely från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Optimizely från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Optimizely**väljer **Optimizely** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Optimizely i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Optimizely baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Optimizely upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Optimizely, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Optimizely Single Sign-On](#configure-optimizely-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Optimizely](#create-optimizely-test-user)**  – du har en motsvarighet för Britta Simon i Optimizely som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Optimizely:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Optimizely** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Optimizely domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://app.optimizely.net/<instance name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > De här värdena är inte verkliga. Du ska uppdatera värdet med faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Programmets Optimizely förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående Optimizely program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

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

6. På den **konfigurera Optimizely** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-optimizely-single-sign-on"></a>Konfigurera Optimizely Single Sign-On

1. Att konfigurera enkel inloggning på **Optimizely** sida, kontakta kontoansvarig Optimizely och ange de hämtade **certifikat (Base64)** och lämpliga kopieras URL: er.

2. Som svar på din e-post ger Optimizely dig logga på URL: en (SP-initierad SSO) och värden för identifierare (Service Provider entitets-ID).

    a. Kopiera den **SP-initierad SSO-URL** angivna genom Optimizely och klistra in i den **inloggning på URL: en** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    b. Kopiera den **entitets-ID för Service Provider** angivna genom Optimizely och klistra in i den **identifierare** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

3. I ett annat webbläsarfönster inloggning till programmets Optimizely.

4. Klicka på du kontonamn i övre högra hörnet och sedan **kontoinställningar**.

    ![Azure AD enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Markera kryssrutan på fliken konto **aktivera SSO** under enkel inloggning i den **översikt** avsnittet.
  
    ![Azure AD enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Optimizely.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Optimizely**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Optimizely**.

    ![Länken Optimizely i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-optimizely-test-user"></a>Skapa Optimizely testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Optimizely.

1. På startsidan, Välj **medarbetare** fliken.

2. Om du vill lägga till nya medarbetare i projektet, klickar du på **nya medarbetare**.
   
    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_10.png)

3. Fyll i e-postadressen och tilldela dem en roll. Klicka på **Bjud in**.

    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_11.png)

4. De får en e-postinbjudan. Med den e-postadressen som de behöver logga in på Optimizely.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Optimizely i åtkomstpanelen, bör det vara loggas in automatiskt till Optimizely som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

