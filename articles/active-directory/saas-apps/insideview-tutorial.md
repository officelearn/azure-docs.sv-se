---
title: 'Självstudiekurs: Azure Active Directory-integrering med InsideView | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100095"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Självstudiekurs: Azure Active Directory-integrering med InsideView

I den här självstudien får du lära dig hur du integrerar InsideView med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till InsideView.
* Du kan aktivera dina användare så att de automatiskt loggas in på InsideView (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med InsideView måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En InsideView-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* InsideView stöder IdP-initierad SSO.

## <a name="add-insideview-from-the-gallery"></a>Lägg till InsideView från galleriet

Om du vill konfigurera integreringen av InsideView i Azure AD måste du lägga till InsideView från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Skriv **InsideView**i sökrutan . Välj **InsideView** i sökresultaten och välj sedan **Lägg till**.

    ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med InsideView med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i InsideView.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med InsideView måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera InsideView enkel inloggning på](#configure-insideview-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en InsideView-testanvändare](#create-an-insideview-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med InsideView:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **InsideView-programintegration:**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/idp-reply.png)

    Ange en URL i det här mönstret i rutan **Svara url:**

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Det här värdet är en platshållare. Du måste använda den faktiska svarsadressen. Kontakta [InsideView-supportteamet](mailto:support@insideview.com) för att få värdet. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **Certifikat (Raw)** enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificateraw.png)

6. I avsnittet **Konfigurera InsideView** kopierar du lämpliga webbadresser baserat på dina krav:

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurera enkel inloggning på InsideView

1. Logga in på webbplatsen InsideView som administratör i ett nytt webbläsarfönster.

1. Högst upp i fönstret väljer du **Admin**, **SingleSignOn-inställningar**och sedan **Lägg till SAML**.
   
   ![SAML-inställningar för enkel inloggning](./media/insideview-tutorial/ic794135.png "SAML-inställningar för enkel inloggning")

1. Gör följande i avsnittet **Lägg till en ny SAML.**

    ![Lägga till ett nytt SAML-avsnitt](./media/insideview-tutorial/ic794136.png "Lägga till ett nytt SAML-avsnitt")

    1. Ange ett namn för konfigurationen i rutan **STS-namn.**

    1. I rutan **SamlP/WS-Fed Oönskad endpoint** klistrar du in värdet **för inloggnings-URL** som du kopierade från Azure-portalen.

    1. Öppna Raw-certifikatet som du hämtade från Azure-portalen. Kopiera innehållet i certifikatet till Urklipp och klistra sedan in innehållet i **sts-certifikatet.**

    1. I rutan Mappning av **CRM-användar-ID** anger du **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. Ange i rutan **Crm-e-postmappning** **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. Ange i rutan Mappning **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**av **CRM-förnamn** .

    1. Ange i rutan Mappning **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**av **Crm-efternamn** .  

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan, väljer **Användare**och väljer sedan **Alla användare:**

    ![Välj alla användare](common/users.png)

2. Välj **Ny användare** högst upp i fönstret:

    ![Välj Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **Användarnamn** anger du **BrittaSimon@\<ditt företag>.\< förlängning>**. (Till exempel BrittaSimon@contoso.com.)

    1. Välj **Visa lösenord**och skriv sedan ned värdet i rutan **Lösenord.**

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till InsideView.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **InsideView**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **InsideView**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-insideview-test-user"></a>Skapa en InsideView-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på InsideView måste du lägga till dem i InsideView. Du måste lägga till dem manuellt.

Om du vill skapa användare eller kontakter i InsideView kontaktar du [Supportteamet för InsideView](mailto:support@insideview.com).

> [!NOTE]
> Du kan använda alla verktyg för att skapa användarkonton eller API som tillhandahålls av InsideView för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen InsideView på åtkomstpanelen bör du automatiskt loggas in i den InsideView-instans som du konfigurerar SSO för. Mer information om Åtkomstpanelen finns i [Komma till åtkomst och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
