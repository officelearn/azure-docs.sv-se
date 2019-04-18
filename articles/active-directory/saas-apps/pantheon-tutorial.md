---
title: 'Självstudier: Azure Active Directory-integrering med Pantheon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pantheon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 30f431c1806c6a0697a6b74b43f1cc35660fc85e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261742"
---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Självstudier: Azure Active Directory-integrering med Pantheon

I den här självstudien får du lära dig hur du integrerar Pantheon med Azure Active Directory (AD Azure).
Integrera Pantheon med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Pantheon.
* Du kan aktivera användarna att vara automatiskt inloggad till Pantheon (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Pantheon, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pantheon enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för pantheon **IDP** -initierad SSO

## <a name="adding-pantheon-from-the-gallery"></a>Att lägga till Pantheon från galleriet

För att konfigurera integrering av Pantheon i Azure AD, som du behöver lägga till Pantheon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pantheon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Pantheon**väljer **Pantheon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Pantheon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pantheon baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Pantheon upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pantheon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Pantheon Single Sign-On](#configure-pantheon-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Pantheon](#create-pantheon-test-user)**  – du har en motsvarighet för Britta Simon i Pantheon som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Pantheon:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Pantheon** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Pantheon domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `urn:auth0:pantheon:<orgname>-SSO`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Pantheon klienten supportteamet](https://pantheon.io/docs/getting-support/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Pantheon program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Pantheon program som förväntar **nameidentifier** mappas med **user.mail**, så måste du redigera attribut mappar genom att klicka på **redigera** ikon och ändra den attributmappning.

    ![mallar](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

7. På den **konfigurera Pantheon** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-pantheon-single-sign-on"></a>Konfigurera Pantheon enkel inloggning

Att konfigurera enkel inloggning på **Pantheon** sida, som du behöver skicka de hämtade **certifikat** och lämpliga kopierade URL: er till [Pantheon supportteamet](https://pantheon.io/docs/getting-support/).

> [!Note]
> Du måste också ange information för e-domänerna och datum tid när du vill aktivera den här anslutningen. Du hittar mer information om den från [här](https://pantheon.io/docs/sso-organizations/)

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pantheon.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Pantheon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Pantheon**.

    ![Länken Pantheon i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pantheon-test-user"></a>Skapa Pantheon testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Pantheon. Följ de stegen nedan för att lägga till användaren i Pantheon. 

>[!NOTE] 
>För enkel inloggning ska fungera användare måste du först skapas i Pantheon.

1. Logga in på Pantheon med autentiseringsuppgifter som administratör.

2. Gå till **organisation** instrumentpanelssidan.
 
3. Klicka på **Personer**.

4. Klicka på **Lägg till användare**.

5. Ange användarens e-postadress.

6. Välj användarens roll.

7. Klicka på **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pantheon i åtkomstpanelen, bör det vara loggas in automatiskt till Pantheon som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

