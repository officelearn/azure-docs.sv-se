---
title: 'Självstudier: Azure Active Directory-integrering med InsideView | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och InsideView.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100095"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Självstudier: Azure Active Directory-integrering med InsideView

I de här självstudierna lär du dig att integrera InsideView med Azure Active Directory (AD Azure).
Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till InsideView.
* Du kan aktivera användarna att logga in automatiskt till InsideView (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med InsideView, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En InsideView-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* InsideView stöder IdP-initierad SSO.

## <a name="add-insideview-from-the-gallery"></a>Lägg till InsideView från galleriet

Om du vill konfigurera integrering av InsideView i Azure AD, som du behöver lägga till InsideView från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **InsideView**. Välj **InsideView** i sökresultatet och välj sedan **Lägg till**.

    ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med InsideView med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i InsideView.

Om du vill konfigurera och testa Azure AD enkel inloggning med InsideView, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera InsideView enkel inloggning](#configure-insideview-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare InsideView](#create-an-insideview-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med InsideView genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/), på sidan InsideView application integration väljer **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan rutan, vidta följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/idp-reply.png)

    I den **svars-URL** anger en URL i det här mönstret:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Det här värdet är en platshållare. Du måste använda faktiska svars-URL. Kontakta den [InsideView supportteamet](mailto:support@insideview.com) att hämta värdet. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **certifikat (Raw)** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificateraw.png)

6. I den **konfigurera InsideView** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav:

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurera InsideView enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in din InsideView företagets webbplats som en administratör.

1. Längst ned i fönstret Välj **Admin**, **SingleSignOn inställningar**, och sedan **lägga till SAML**.
   
   ![SAML enkel inloggning inställningar](./media/insideview-tutorial/ic794135.png "SAML enkel inloggning-inställningar")

1. I den **lägga till en ny SAML** avsnittet, vidta följande steg.

    ![Lägg till en ny SAML-avsnitt](./media/insideview-tutorial/ic794136.png "Lägg till en ny SAML-avsnitt")

    1. I den **STS Name** anger du ett namn för din konfiguration.

    1. I den **SamlP/WS-Fed oombedda EndPoint** rutan, klistra in den **inloggnings-URL** värde som du kopierade från Azure-portalen.

    1. Öppna Raw certifikatet som du laddade ned från Azure-portalen. Kopiera innehållet i certifikatet till Urklipp och klistra in innehållet i den **STS Certificate** box.

    1. I den **Crm användarmappning för Id** anger **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. I den **Crm e-mappning** anger **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. I den **Crm förnamn mappning** anger **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. I den **Crm lastName mappning** anger **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst i fönstret:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >** . (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst InsideView.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **InsideView**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **InsideView**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** knappen längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** knappen längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-insideview-test-user"></a>Skapa en InsideView testanvändare

Om du vill aktivera Azure AD-användare att logga in på InsideView som du behöver lägga till dem i InsideView. Du måste lägga till dem manuellt.

För att skapa användare eller kontakter i InsideView, kontakta den [InsideView supportteamet](mailto:support@insideview.com).

> [!NOTE]
> Du kan använda valfri användare verktyg för skapande av konto eller API tillhandahålls av InsideView att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen InsideView i åtkomstpanelen, bör det vara loggas in automatiskt till InsideView-instansen som du ställer in enkel inloggning. Läs mer om åtkomstpanelen [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
