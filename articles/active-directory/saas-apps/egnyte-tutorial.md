---
title: 'Självstudier: Azure Active Directory-integrering med Egnyte | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ff1718211fa298e4daee694795a0b2438033fe2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838143"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Självstudier: Azure Active Directory-integrering med Egnyte

I den här självstudien lär du dig att integrera Egnyte med Azure Active Directory (Azure AD).
När du integrerar Egnyte med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Egnyte från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Egnyte (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Egnyte behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Egnyte-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Egnyte stöder **SP**-initierad enkel inloggning

## <a name="adding-egnyte-from-the-gallery"></a>Lägga till Egnyte från galleriet

För att konfigurera integrering av Egnyte i Azure AD behöver du lägga till Egnyte från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Egnyte från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Egnyte** i sökrutan, välj **Egnyte** i resultatpanelen och klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![Egnyte i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Egnyte baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Egnyte upprättas.

För att konfigurera och testa enkel inloggning med Azure AD för Egnyte, måste du utföra följande:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Egnyte](#configure-egnyte-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Egnyte-testanvändare](#create-egnyte-test-user)** – för att ha en motsvarighet för Britta Simon i Egnyte som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Egnyte:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Egnyte** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Egnyte-domäner och -URL:er](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för Egnyte-klienten](https://www.egnyte.com/corp/contact_egnyte.html) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

5. I avsnittet **Konfigurera Egnyte** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-egnyte-single-sign-on"></a>Konfigurera enkel inloggning med Egnyte

1. Öppna ett nytt webbläsarfönster och logga in på din Egnyte-företagswebbplats som administratör.

2. Klicka på **Inställningar**.
   
    ![Inställningar](./media/egnyte-tutorial/ic787819.png "Inställningar")

3. Klicka på **Inställningar** på menyn.

    ![Inställningar](./media/egnyte-tutorial/ic787820.png "Inställningar")

4. Klicka på fliken **Konfiguration** och sedan på **Säkerhet**.

    ![Säkerhet](./media/egnyte-tutorial/ic787821.png "Säkerhet")

5. Gör följande i avsnittet **Enkel inloggningsautentisering**:

    ![Enkel inloggningsautentisering](./media/egnyte-tutorial/ic787822.png "Enkel inloggningsautentisering")   
    
    a. För **Enkel inloggningsautentisering** väljer du **SAML 2.0**.
   
    b. Som **identitetsprovider** väljer du **AzureAD**.
   
    c. Klistra in **inloggnings-URL:en** som du har kopierat från Azure-portalen till textrutan **Inloggnings-URL för identitetsprovider**.
   
    d. Klistra in **Azure AD-identifieraren** som du har kopierat från Azure-portalen till textrutan för **entitets-ID för identitetsprovider**.
      
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **Identity Provider Certificate**  (Certifikat för identitetsprovider).
   
    f. Välj **e-postadress** som **standardanvändarmappning**.
   
    g. Välj **inaktiverad** för **Use domain-specific issuer value** (Använd domänspecifikt utfärdarvärde).
   
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
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Egnyte.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Egnyte**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Egnyte** i programlistan.

    ![Egnyte-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-egnyte-test-user"></a>Skapa Egnyte-testanvändare

För att Azure AD-användare ska kunna logga in i Egnyte måste de vara etablerade i Egnyte. Etablering utförs manuellt med Egnyte.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in som administratör på din **Egnyte**-företagswebbplats.

2. Gå till **Settings (Inställningar) \> Users & Groups (Användare och grupper)**.

3. Klicka på **Add New User** (Lägg till ny användare) och välj sedan vilken typ av användare som du vill lägga till.
   
    ![Användare](./media/egnyte-tutorial/ic787824.png "Användare")

4. I avsnittet **New Power User** (Ny privilegierad användare) utför du följande steg:
    
    ![Ny standardanvändare](./media/egnyte-tutorial/ic787825.png "Ny standardanvändare")   

    a. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon\@contoso.com**.

    b. I textrutan för **användarnamn** anger du användarnamnet för användaren, till exempel **Brittasimon**.

    c. Välj **Single Sign-On** (Enkel inloggning) som **Authentication Type** (Autentiseringstyp).
   
    d. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får en e-postavisering.
    >

>[!NOTE]
>Du kan använda valfria verktyg eller API:er från Egnyte för att etablera AAD-användarkonton.
>

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Egnyte-panelen i åtkomstpanelen bör du automatiskt loggas in på Egnyte som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

