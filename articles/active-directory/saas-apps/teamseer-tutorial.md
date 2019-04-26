---
title: 'Självstudier: Azure Active Directory-integrering med TeamSeer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: ef11011ec37f72003dd6c238ce748d417bfcd505
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315923"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Självstudier: Azure Active Directory-integrering med TeamSeer

I den här självstudien får du lära dig hur du integrerar TeamSeer med Azure Active Directory (AD Azure).
Integrera TeamSeer med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TeamSeer.
* Du kan aktivera användarna att vara automatiskt inloggad till TeamSeer (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TeamSeer, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* TeamSeer enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för TeamSeer **SP** -initierad SSO

## <a name="adding-teamseer-from-the-gallery"></a>Att lägga till TeamSeer från galleriet

För att konfigurera integrering av TeamSeer i Azure AD, som du behöver lägga till TeamSeer från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TeamSeer från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **TeamSeer**väljer **TeamSeer** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![TeamSeer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TeamSeer baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TeamSeer upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med TeamSeer, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare TeamSeer](#create-teamseer-test-user)**  – du har en motsvarighet för Britta Simon i TeamSeer som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med TeamSeer:

1. I den [Azure-portalen](https://portal.azure.com/)på den **TeamSeer** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TeamSeer domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [TeamSeer klienten supportteamet](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera TeamSeer** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-teamseer-single-sign-on"></a>Konfigurera TeamSeer Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din TeamSeer företagets webbplats som administratör.

1. Gå till **HR Admin**.

    ![HR Admin](./media/teamseer-tutorial/ic789634.png "HR Admin")

1. Klicka på **installationsprogrammet**.

    ![Konfiguration](./media/teamseer-tutorial/ic789635.png "Konfiguration")

1. Klicka på **ställa in information om SAML-provider**.

    ![SAML-inställningar](./media/teamseer-tutorial/ic789636.png "SAML-inställningar")

1. Utför följande steg i informationsavsnittet för SAML-providern:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789637.png "SAML-inställningar")

    a. I den **URL** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    b. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den i till Urklipp och klistra in den till den **IdP offentligt certifikat** textrutan.

1. Utför följande steg för att slutföra konfigurationen av SAML-providern:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789638.png "SAML-inställningar")

    a. I den **testa e-postadresser**, Skriv test-användarens e-postadress.
  
    b. I den **utfärdare** textrutan skriver utfärdar-URL för tjänstleverantören.
  
    c. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TeamSeer.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **TeamSeer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **TeamSeer**.

    ![Länken TeamSeer i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-teamseer-test-user"></a>Skapa TeamSeer testanvändare

Om du vill aktivera Azure AD-användare att logga in på TeamSeer, måste de vara etablerade i att ShiftPlanning. När det gäller TeamSeer är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **TeamSeer** företagets plats som administratör.

1. Gå till **HR Admin \> användare** och klicka sedan på **kör guiden Ny användare**.

    ![HR Admin](./media/teamseer-tutorial/ic789640.png "HR Admin")

1. I den **användarinformation** avsnittet, utför följande steg:

    ![Användarinformation](./media/teamseer-tutorial/ic789641.png "Användarinformation")

    a. Skriv den **Förnamn**, **efternamn**, **användarnamn (e-postadress)** av en giltig Azure AD-konto som du vill etablera i till relaterade textrutor.
  
    b. Klicka på **Nästa**.

1. Följ den på skärmen för att lägga till en ny användare och klicka på **Slutför**.

> [!NOTE]
> Du kan använda alla andra TeamSeer användare konto verktyg för att skapa eller API: er som tillhandahålls av TeamSeer att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TeamSeer i åtkomstpanelen, bör det vara loggas in automatiskt till TeamSeer som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
