---
title: 'Självstudiekurs: Azure Active Directory-integrering med UserVoice | Microsoft-dokument'
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
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232004"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Självstudiekurs: Azure Active Directory-integrering med UserVoice

I den här självstudien får du lära dig hur du integrerar UserVoice med Azure Active Directory (Azure AD).
Genom att integrera UserVoice med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till UserVoice.
* Du kan aktivera dina användare så att de automatiskt loggas in på UserVoice (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med UserVoice behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* UserVoice enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UserVoice stöder **SP** initierad SSO

## <a name="adding-uservoice-from-the-gallery"></a>Lägga till UserVoice från galleriet

Om du vill konfigurera integreringen av UserVoice i Azure AD måste du lägga till UserVoice från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till UserVoice från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **UserVoice**i sökrutan och välj **UserVoice** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![UserVoice i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med UserVoice baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i UserVoice upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med UserVoice måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera UserVoice Single Sign-On](#configure-uservoice-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa UserVoice-testanvändare](#create-uservoice-test-user)** – om du vill ha en motsvarighet till Britta Simon i UserVoice som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med UserVoice:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **UserVoice-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![UserVoice-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [UserVoice Client supportteam](https://www.uservoice.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **tumavtrycket** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera UserVoice.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-uservoice-single-sign-on"></a>Konfigurera UserVoice Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på uservoice-företagets webbplats som administratör.

2. Klicka på **Inställningar**i verktygsfältet högst upp och välj sedan **Webbportal** på menyn.
   
    ![Avsnittet Inställningar på appsidan](./media/uservoice-tutorial/ic777519.png "Inställningar")

3. Öppna dialogrutan **Redigera autentisering** i avsnittet **Användarautentisering** **på** fliken **Webbportal.**
   
    ![Fliken Webbportal](./media/uservoice-tutorial/ic777520.png "Webbportalen")

4. Gör följande på dialogrutan **Redigera användarautentisering:**
   
    ![Redigera användarautentisering](./media/uservoice-tutorial/ic777521.png "Redigera användarautentisering")
   
    a. Klicka på **Enkel inloggning (SSO)**.
 
    b. Klistra in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen till **SSO Remote Sign-In textbox.**

    c. Klistra in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen till **textrutan SSO Remote Sign-Out**.
 
    d. Klistra in **tumavtrycksvärdet** , som du har kopierat från Azure-portalen i den **aktuella certifikatet SHA1 fingeravtryckstextrutan.**
    
    e. Klicka på **Spara autentiseringsinställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till UserVoice.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **UserVoice**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **UserVoice**i programlistan .

    ![Länken UserVoice i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-uservoice-test-user"></a>Skapa UserVoice-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på UserVoice måste de etableras i UserVoice. När det gäller UserVoice är etablering en manuell uppgift.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **UserVoice-klient.**

2. Gå till **Inställningar**.
   
    ![Inställningar](./media/uservoice-tutorial/ic777811.png "Inställningar")

3. Klicka på **Allmänt**.

4. Klicka på **Agenter och behörigheter**.
   
    ![Agenter och behörigheter](./media/uservoice-tutorial/ic777812.png "Agenter och behörigheter")

5. Klicka på **Lägg till administratörer**.
   
    ![Lägg till administratörer](./media/uservoice-tutorial/ic777813.png "Lägg till administratörer")

6. Gör följande i dialogrutan **Bjud in administratörer:**
   
    ![Bjud in administratörer](./media/uservoice-tutorial/ic777814.png "Bjud in administratörer")
   
    a. Skriv e-postadressen till det konto som du vill etablera i textrutan E-post och klicka sedan på **Lägg till**.
   
    b. Klicka på **Bjud in**.

> [!NOTE]
> Du kan använda andra Användarröst användarkonto skapande verktyg eller API: er som tillhandahålls av UserVoice att etablera Azure AD användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen UserVoice på åtkomstpanelen ska du automatiskt loggas in på den UserVoice som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

