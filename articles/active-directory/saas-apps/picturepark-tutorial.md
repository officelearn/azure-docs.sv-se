---
title: 'Självstudier: Azure Active Directory-integrering med Picturepark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 7a4db08a342bc4004d4957e26353ef810ebae4d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689593"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Självstudier: Azure Active Directory-integrering med Picturepark

I den här självstudien får du lära dig hur du integrerar Picturepark med Azure Active Directory (AD Azure).
Integrera Picturepark med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Picturepark.
* Du kan aktivera användarna att vara automatiskt inloggad till Picturepark (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Picturepark, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Picturepark enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Picturepark **SP** -initierad SSO

## <a name="adding-picturepark-from-the-gallery"></a>Att lägga till Picturepark från galleriet

För att konfigurera integrering av Picturepark i Azure AD, som du behöver lägga till Picturepark från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Picturepark från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Picturepark**väljer **Picturepark** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Picturepark i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Picturepark baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Picturepark upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Picturepark, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Picturepark Single Sign-On](#configure-picturepark-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Picturepark](#create-picturepark-test-user)**  – du har en motsvarighet för Britta Simon i Picturepark som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Picturepark:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Picturepark** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Picturepark domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.picturepark.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Picturepark klienten supportteamet](https://picturepark.com/about/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. På den **konfigurera Picturepark** avsnittet, kopiera den lämpliga URL: er enligt dina behov. För **inloggnings-URL**, använder du värdet med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-id för Azure AD-prenumeration.

    ![Kopiera konfigurations-URL:er](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-picturepark-single-sign-on"></a>Konfigurera Picturepark Single Sign-On

1. Logga in på webbplatsen Picturepark företag som en administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp, klickar du på **Administrationsverktyg**, och klicka sedan på **hanteringskonsolen**.
   
    ![Konsolen Grupprinciphantering](./media/picturepark-tutorial/ic795062.png "-hanteringskonsolen")

3. Klicka på **autentisering**, och klicka sedan på **identitetsprovidrar**.
   
    ![Autentisering](./media/picturepark-tutorial/ic795063.png "Autentisering")

4. I den **identitet providerkonfigurationen** avsnittet, utför följande steg:
   
    ![Identitet providerkonfigurationen](./media/picturepark-tutorial/ic795064.png "identitet Providerkonfiguration")
   
    a. Klicka på **Lägg till**.
  
    b. Skriv ett namn för din konfiguration.
   
    c. Välj **ange som standard**.
   
    d. I **utfärdare-URI** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.
   
    e. I **betrodda utfärdare tumavtryck** textrutan klistra in värdet för **tumavtryck** som du har kopierat från **SAML-signeringscertifikat** avsnittet. 

5. Klicka på **JoinDefaultUsersGroup**.

6. Ange den **e-postadress** attribut i den **anspråk** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` och klicka på **spara**.

      ![Konfigurationen](./media/picturepark-tutorial/ic795065.png "konfiguration")

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Picturepark.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Picturepark**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Picturepark**.

    ![Länken Picturepark i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-picturepark-test-user"></a>Skapa Picturepark testanvändare

För att aktivera Azure AD-användare att logga in på Picturepark, måste de etableras i Picturepark. När det gäller Picturepark är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Picturepark** klient.

1. I verktygsfältet högst upp, klickar du på **Administrationsverktyg**, och klicka sedan på **användare**.
   
    ![Användare](./media/picturepark-tutorial/ic795067.png "Användare")

1. I den **översikt över användare** fliken **New**.
   
    ![Användarhantering](./media/picturepark-tutorial/ic795068.png "Användarhantering")

1. På den **Create User** dialogrutan utför följande steg på en giltig användare för Azure Active Directory som du vill att etablera:
   
    ![Skapa användare](./media/picturepark-tutorial/ic795069.png "Skapa användare")
   
    a. I den **e-postadress** textrutan skriver den **e-postadress** användarens `BrittaSimon@contoso.com`.  
   
    b. I den **lösenord** och **Bekräfta lösenord** textrutor, skriver den **lösenord** av BrittaSimon. 
   
    c. I den **Förnamn** textrutan skriver den **Förnamn** användarens **Britta**. 
   
    d. I den **efternamn** textrutan skriver den **efternamn** användarens **Simon**.
   
    e. I den **företagets** textrutan skriver den **företagsnamn** för användaren. 
   
    f. I den **land** textrutan, väljer den **land** för användaren.
  
    g. I den **ZIP** textrutan skriver den **postnummer** på ort.
   
    h. I den **Stad** textrutan skriver den **Ortnamn** för användaren.

    i. Välj en **språk**.
   
    j. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda alla andra Picturepark användare konto verktyg för att skapa eller API: er som tillhandahålls av Picturepark att etablera användarkonton i Azure AD.
>

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Picturepark i åtkomstpanelen, bör det vara loggas in automatiskt till Picturepark som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

