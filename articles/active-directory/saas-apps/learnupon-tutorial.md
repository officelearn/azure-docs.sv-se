---
title: 'Självstudiekurs: Azure Active Directory-integrering med LearnUpon | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098244"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Självstudiekurs: Azure Active Directory-integrering med LearnUpon

I den här självstudien får du lära dig hur du integrerar LearnUpon med Azure Active Directory (Azure AD).
Genom att integrera LearnUpon med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LearnUpon.
* Du kan aktivera dina användare så att de automatiskt loggas in på LearnUpon (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med LearnUpon behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LearnUpon enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.


* LearnUpon stöder **IDP-initierad** SSO

* LearnUpon stöder just **in time-användares** etablering


## <a name="adding-learnupon-from-the-gallery"></a>Lägga till LearnUpon från galleriet

Om du vill konfigurera integreringen av LearnUpon i Azure AD måste du lägga till LearnUpon från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till LearnUpon från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **LearnUpon**i sökrutan och välj **LearnUpon** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![LearnUpon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med LearnUpon baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i LearnUpon upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med LearnUpon måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LearnUpon Enkel inloggning](#configure-learnupon-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LearnUpon-testanvändare](#create-learnupon-test-user)** – om du vill ha en motsvarighet till Britta Simon i LearnUpon som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med LearnUpon:

1. Välj Enkel inloggning på sidan **LearnUpon-programintegrering** på [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Intänga domän och webbadresser med enkel inloggning](common/idp-reply.png)

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [LearnUpon Client supportteam](https://www.learnupon.com/features/support/) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** letar du reda på **tumavtrycket** - Detta läggs till i dina LearnUpon SAML-inställningar.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera LearnUpon.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-learnupon-single-sign-on"></a>Konfigurera Inakteringsbaserad inloggning

1. Öppna en annan webbläsarinstans och logga in på LearnUpon med ett administratörskonto.

1. Klicka på **fliken Inställningar.**

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klicka på **Enkel inloggning - SAML**och sedan på **Allmänna inställningar** för att konfigurera SAML-inställningar.
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Gör följande i avsnittet **Allmänna inställningar:**
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Välj **Aktiverad**.

    b. Välj **Version** som **2.0**.

    c. Välj **Hoppa över villkor** som **nr**.

    d. I **sms:et för PARAM-namnet i SAML Post** skriver du namnet på parametern för begäran efter i SAML-konsument-URL:en som anges ovan och som innehåller SAML-kontrollen som ska verifieras och autentiseras , till exempel **SAMLResponse**.

    e. I textrutan **Namnidentifierare format** skriver du det värde som anger var i ditt SAML-kontroll `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`användarnas identifierare (E-postadress) finns, till exempel .
  
    f. I textrutan **Identifiera providerplats** skriver du det värde som anger vart användarna skickas till om de klickar på din uppladdade ikon från inloggningsskärmen för Azure-portalen.
  
    g. I textrutan **Logga ut URL** klistrar du in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen.

    h. Klicka på **Hantera fingeravtryck**och ladda sedan upp fingeravtrycket på det hämtade certifikatet.

1. Klicka på **Användarinställningar**och utför sedan följande:

     ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. I textrutan **Förnamnsidentifieringsformat** skriver du det värde som talar om för oss `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`var i ditt SAML-kontroll användarnas förnamn finns, till exempel: .
  
    b. I textrutan **Förnamnsidentifieringsformat** skriver du det värde som talar om för oss `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`var i ditt SAML-kontroll användarnas efternamn finns, till exempel: .

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till LearnUpon.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **LearnUpon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **LearnUpon**i programlistan .

    ![Länken LearnUpon i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-learnupon-test-user"></a>Skapa LearnUpon-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i LearnUpon. LearnUpon stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LearnUpon skapas en ny efter autentisering. Om du behöver skapa en användare manuellt måste du kontakta [LearnUpons supportteam](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LearnUpon på åtkomstpanelen bör du automatiskt loggas in på den LearnUpon som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)