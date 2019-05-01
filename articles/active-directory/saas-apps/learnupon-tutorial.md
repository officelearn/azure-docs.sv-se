---
title: 'Självstudier: Azure Active Directory-integrering med LearnUpon | Microsoft Docs'
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
ms.openlocfilehash: 0a8b894a5b790321c0af8527bf77e07b9923d577
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686768"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Självstudier: Azure Active Directory-integrering med LearnUpon

I den här självstudien får du lära dig hur du integrerar LearnUpon med Azure Active Directory (AD Azure).
Integrera LearnUpon med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LearnUpon.
* Du kan aktivera användarna att vara automatiskt inloggad till LearnUpon (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LearnUpon, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LearnUpon enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.


* Har stöd för LearnUpon **IDP** -initierad SSO

* Har stöd för LearnUpon **Just In Time** etableringen av användare


## <a name="adding-learnupon-from-the-gallery"></a>Att lägga till LearnUpon från galleriet

För att konfigurera integrering av LearnUpon i Azure AD, som du behöver lägga till LearnUpon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LearnUpon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **LearnUpon**väljer **LearnUpon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![LearnUpon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LearnUpon baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LearnUpon upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LearnUpon, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare LearnUpon](#create-learnupon-test-user)**  – du har en motsvarighet för Britta Simon i LearnUpon som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med LearnUpon:

1. I den [Azure-portalen](https://portal.azure.com/)på den **LearnUpon** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LearnUpon domän och URL: er med enkel inloggning för information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [LearnUpon klienten supportteamet](https://www.learnupon.com/features/support/) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** , letar du upp den **TUMAVTRYCK** – detta kommer att läggas till dina LearnUpon SAML-inställningar.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **konfigurera LearnUpon** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-learnupon-single-sign-on"></a>Konfigurera LearnUpon Single Sign-On

1. Öppna en annan instans av webbläsare och logga in till LearnUpon med ett administratörskonto.

1. Klicka på den **inställningar** fliken.

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klicka på **Single Sign On - SAML**, och klicka sedan på **allmänna inställningar** att konfigurera SAML-inställningar.
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. I den **allmänna inställningar** avsnittet, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Välj **aktiverat**.

    b. Välj **Version** som **2.0**.

    c. Välj **hoppa över villkor** som **nr**.

    d. I den **SAML-Token efter parameternamn** textrutan skriver namnet på begäran efter parameter till URL för SAML-konsument som nämns ovan som innehåller SAML-försäkran som ska verifieras och autentiseras – till exempel **SAMLResponse** .

    e. I den **Format för namn på identifierare** textrutan typ som det värde som anger var i SAML-försäkran användare identifierare (e-postadress) finns – till exempel `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. I den **identifiera Providerplats** textrutan skriver du det värde som anger där användarna skickas till om de klickar på överförda ikonen från dina Azure-portalen inloggningsskärmen.
  
    g. I den **utloggnings-URL** textrutan klistra in den **URL för utloggning** värde, som du har kopierat från Azure-portalen.

    h. Klicka på **hantera finger utskrifter**, och sedan överför fingeravtryck på nedladdade certifikatet.

1. Klicka på **användarinställningar**, och utför följande steg:

     ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. I den **förnamn identifierare Format** textrutan typ som det värde som talar om för oss var i SAML-försäkran användare firstname finns – till exempel: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. I den **senaste Format för namn på identifierare** textrutan typ som det värde som talar om för oss var i SAML-försäkran användare lastname finns – till exempel: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LearnUpon.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **LearnUpon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **LearnUpon**.

    ![Länken LearnUpon i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-learnupon-test-user"></a>Skapa LearnUpon testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i LearnUpon. LearnUpon stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LearnUpon, skapas en ny efter autentisering. Om du vill skapa en användare manuellt kan du behöva kontakta [LearnUpon supportteamet](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LearnUpon i åtkomstpanelen, bör det vara loggas in automatiskt till LearnUpon som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)