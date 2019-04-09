---
title: 'Självstudier: Azure Active Directory-integrering med Mozy Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: a0f21165af0bcbd8bda28f0eae20d3ee837f3be9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275665"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Självstudier: Azure Active Directory-integrering med Mozy Enterprise

Lär dig hur du integrerar Mozy Enterprise med Azure Active Directory (AD Azure) i den här självstudien.
Integrera Mozy Enterprise med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mozy Enterprise.
* Du kan aktivera användarna att vara automatiskt inloggad till Mozy Enterprise (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mozy Enterprise, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Mozy Enterprise **SP** -initierad SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Att lägga till Mozy Enterprise från galleriet

Om du vill konfigurera integreringen av Mozy Enterprise till Azure AD, som du behöver lägga till Mozy Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mozy Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Mozy Enterprise**väljer **Mozy Enterprise** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Mozy Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Mozy Enterprise upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Mozy-Enterprise](#configure-mozy-enterprise-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mozy Enterprise testanvändare](#create-mozy-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i Mozy företag som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Mozy Enterprise:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Mozy Enterprise** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mozy Enterprise domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Mozy Enterprise Client supportteamet](http://support.mozy.com/) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Mozy Enterprise** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurera Mozy Enterprise enkel inloggning

1. Logga in på ditt företag Mozy Företagsplats som administratör i ett annat webbläsarfönster.

2. I den **Configuration** klickar du på **autentiseringsprincip**.
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777314.png "autentiseringsprincip")

3. På den **autentiseringsprincip** avsnittet, utför följande steg:
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777315.png "autentiseringsprincip")
   
    a. Välj **katalogtjänsten** som **Provider**.
   
    b. Välj **använder LDAP Push**.
   
    c. Klicka på fliken **SAML-autentisering**.
   
    d. Klistra in **inloggnings-URL**, som du har kopierat från Azure-portalen till den **autentiserings-URL för** textrutan.
   
    e. Klistra in **Azure AD-identifierare**, som du har kopierat från Azure-portalen till den **SAML Endpoint** textrutan.
   
    f. Öppna din hämtade Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **SAML-certifikatet** textrutan.
   
    g. Välj **aktivera SSO för administratörer att logga in med sina nätverksinloggningsuppgifter**.
   
    h. Klicka på **Spara ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mozy Enterprise.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Mozy Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Mozy Enterprise**.

    ![Länken Mozy Enterprise i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mozy-enterprise-test-user"></a>Skapa Mozy Enterprise testanvändare

För att aktivera Azure AD-användare att logga in på Mozy Enterprise, måste de etableras i Mozy Enterprise. När det gäller Mozy Enterprise är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Mozy Enterprise användare konto verktyg för att skapa eller API: er som tillhandahålls av Mozy Enterprise att etablera AAD-användarkonton.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på din **Mozy Enterprise** klient.

2. Klicka på **användare**, och klicka sedan på **Lägg till ny användare**.
   
    ![Användare](./media/mozy-enterprise-tutorial/ic777317.png "Användare")
   
    >[!NOTE]
    >Den **Lägg till ny användare** alternativet visas bara om **Mozy** väljs som providern under **autentiseringsprincip**. Om SAML-autentisering har konfigurerats, sedan läggs användarna till automatiskt på sin första inloggning via enkel inloggning på.
    
3. I användardialogrutan ny utför du följande steg:
   
    ![Lägga till användare](./media/mozy-enterprise-tutorial/ic777318.png "lägga till användare")
   
    a. Från den **välja en grupp** väljer du en grupp.
   
    b. Från den **vilken typ av användare** väljer du en typ.
   
    c. I den **användarnamn** textrutan skriver du namnet på Azure AD-användare.
   
    d. I den **e-post** textrutan skriver du e-postadressen för Azure AD-användare.
   
    e. Välj **skicka användarens e-postadress med instruktionen**.
   
    f. Klicka på **lägga till användare**.

     >[!NOTE]
     > När du har skapat användaren skickas ett e-postmeddelande till Azure AD-användare som innehåller en länk för att bekräfta kontot innan det blir aktiv.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mozy Enterprise i åtkomstpanelen, bör det vara loggas in automatiskt Mozy Enterprise som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

