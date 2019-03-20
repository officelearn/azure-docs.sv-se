---
title: 'Självstudier: Azure Active Directory-integrering med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 4a9141c0840ff5d962adceb0d0481102cff868d4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897358"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Självstudier: Azure Active Directory-integrering med New Relic

I den här självstudien får du lära dig hur du integrerar New Relic med Azure Active Directory (AD Azure).
Integrera New Relic med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till New Relic.
* Du kan aktivera användarna att vara automatiskt inloggad till New Relic (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med New Relic, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Nya Relic enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* New Relic stöder **SP** -initierad SSO

## <a name="adding-new-relic-from-the-gallery"></a>Lägger till New Relic från galleriet

Om du vill konfigurera integreringen av New Relic till Azure AD, som du behöver lägga till New Relic från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till nya Relic från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **New Relic**väljer **New Relic** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![New Relic i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med New Relic baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i New Relic upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med New Relic, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera nya Relic enkel inloggning](#configure-new-relic-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare i New Relic](#create-new-relic-test-user)**  – du har en motsvarighet för Britta Simon i New Relic som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med New Relic:

1. I den [Azure-portalen](https://portal.azure.com/)på den **New Relic** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Nya Relic domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – se till att ersätta ditt eget New Relic-konto-ID.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL: `rpm.newrelic.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **ställa in New Relic** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-new-relic-single-sign-on"></a>Konfigurera nya Relic enkel inloggning

1. I ett annat webbläsarfönster, loggar du in på ditt **New Relic** företagets plats som administratör.

2. Klicka på menyn längst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797036.png "kontoinställningar")

3. Klicka på den **säkerhet och autentisering** fliken och klicka sedan på den **enkel inloggning** fliken.
   
    ![Enkel inloggning](./media/new-relic-tutorial/ic797037.png "Enkel inloggning")

4. Utför följande steg på sidan SAML dialogrutan:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klicka på **Välj fil** att ladda upp din hämtade Azure Active Directory-certifikatet.

    b. I den **fjärrinloggning URL** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.
   
    c. I den **utloggning landing URL** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    d. Klicka på **spara mina ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till New Relic.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **New Relic**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **New Relic**.

    ![New Relic-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-new-relic-test-user"></a>Skapa nya Relic testanvändare

För att aktivera Azure Active Directory-användare att logga in på New Relic, måste de etableras i New Relic. När det gäller New Relic är etablering en manuell aktivitet.

**Om du vill konfigurera ett användarkonto till New Relic, utför du följande steg:**

1. Logga in på din **New Relic** företagets plats som administratör.

2. Klicka på menyn längst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797040.png "kontoinställningar")

3. I den **konto** fönstret till vänster, klickar du på **sammanfattning**, och klicka sedan på **Lägg till användare**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797041.png "kontoinställningar")

4. På den **aktiva användare** dialogrutan utför följande steg:
   
    ![Aktiva användare](./media/new-relic-tutorial/ic797042.png "aktiva användare")
   
    a. I den **e-post** textrutan skriver du en giltig Azure Active Directory-användare som du vill etablera e-postadress.

    b. Som **rollen** Välj **användaren**.

    c. Klicka på **Lägg till användaren**.

>[!NOTE]
>Du kan använda New Relic användaren-konto skapas verktyg från någon annan eller API: er fås från New Relic för att etablera AAD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på New Relic-panelen i åtkomstpanelen, bör det vara loggas in automatiskt till New Relic som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

