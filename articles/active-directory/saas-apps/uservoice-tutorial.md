---
title: 'Självstudier: Azure Active Directory-integrering med UserVoice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: dbd7189b1761a9ea88ce32dae3d7b45a88301ff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338775"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Självstudier: Azure Active Directory-integrering med UserVoice

I den här självstudien får du lära dig hur du integrerar UserVoice med Azure Active Directory (AD Azure).
Integrera UserVoice med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till UserVoice.
* Du kan aktivera användarna att vara automatiskt inloggad till UserVoice (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med UserVoice, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* UserVoice enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för UserVoice **SP** -initierad SSO

## <a name="adding-uservoice-from-the-gallery"></a>Att lägga till UserVoice från galleriet

För att konfigurera integrering av UserVoice i Azure AD, som du behöver lägga till UserVoice från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till UserVoice från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **UserVoice**väljer **UserVoice** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![UserVoice i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med UserVoice baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i UserVoice upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med UserVoice, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera UserVoice Single Sign-On](#configure-uservoice-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare UserVoice](#create-uservoice-test-user)**  – du har en motsvarighet för Britta Simon i UserVoice som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med UserVoice:

1. I den [Azure-portalen](https://portal.azure.com/)på den **UserVoice** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![UserVoice-domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [UserVoice klienten supportteamet](https://www.uservoice.com/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. På den **konfigurera UserVoice** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-uservoice-single-sign-on"></a>Konfigurera UserVoice Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din UserVoice-webbplatsen för företaget som administratör.

2. I verktygsfältet högst upp, klickar du på **inställningar**, och välj sedan **webbportalen** på menyn.
   
    ![Inställningar på App-sida](./media/uservoice-tutorial/ic777519.png "inställningar")

3. På den **webbportalen** fliken den **användarautentisering** klickar du på **redigera** att öppna den **redigera användarautentisering** dialogrutan sidan.
   
    ![Webbportalen fliken](./media/uservoice-tutorial/ic777520.png "webbportalen")

4. På den **redigera användarautentisering** dialogrutan utför följande steg:
   
    ![Redigera användarautentisering](./media/uservoice-tutorial/ic777521.png "redigera användarautentisering")
   
    a. Klicka på **enkel inloggning (SSO)**.
 
    b. Klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen till den **SSO Remote inloggning** textrutan.

    c. Klistra in den **URL för utloggning** värde, som du har kopierat från Azure-portalen till den **SSO Remote utloggning textrutan**.
 
    d. Klistra in den **tumavtryck** värde, som du har kopierat från Azure-portalen till den **aktuella certifikat SHA1-fingeravtryck** textrutan.
    
    e. Klicka på **spara autentiseringsinställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till UserVoice.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **UserVoice**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **UserVoice**.

    ![UserVoice-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-uservoice-test-user"></a>Skapa testanvändare i UserVoice

Om du vill aktivera Azure AD-användare att logga in till UserVoice, måste de etableras i UserVoice. När det gäller UserVoice är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **UserVoice** klient.

2. Gå till **Inställningar**.
   
    ![Inställningar](./media/uservoice-tutorial/ic777811.png "Inställningar")

3. Klicka på **Allmänt**.

4. Klicka på **agenter och behörigheter**.
   
    ![Agenter och behörigheter](./media/uservoice-tutorial/ic777812.png "agenter och behörigheter")

5. Klicka på **lägga till administratörer**.
   
    ![Lägga till administratörer](./media/uservoice-tutorial/ic777813.png "lägga till administratörer")

6. På den **bjuda in administratörer** dialogrutan utför följande steg:
   
    ![Bjud in administratörer](./media/uservoice-tutorial/ic777814.png "bjuda in administratörer")
   
    a. I textrutan för e-postmeddelanden, skriver du e-postadressen för det konto som du vill etablera och klicka sedan på **Lägg till**.
   
    b. Klicka på **Bjud in**.

> [!NOTE]
> Du kan använda alla andra UserVoice användare konto verktyg för att skapa eller API: er som tillhandahålls av UserVoice att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen UserVoice i åtkomstpanelen, bör det vara loggas in automatiskt till UserVoice där du konfigurerar enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

