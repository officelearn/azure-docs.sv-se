---
title: 'Självstudier: Azure Active Directory-integrering med Yodeck | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 8fce54cba0b0b64390256bc9669821fa582c2e8a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920118"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Självstudier: Azure Active Directory-integrering med Yodeck

I den här självstudien får du lära dig hur du integrerar Yodeck med Azure Active Directory (AD Azure).
Integrera Yodeck med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Yodeck.
* Du kan aktivera användarna att vara automatiskt inloggad till Yodeck (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Yodeck, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Yodeck enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Yodeck **SP** och **IDP** -initierad SSO

## <a name="adding-yodeck-from-the-gallery"></a>Att lägga till Yodeck från galleriet

För att konfigurera integrering av Yodeck i Azure AD, som du behöver lägga till Yodeck från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Yodeck från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Yodeck**väljer **Yodeck** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Yodeck i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Yodeck baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Yodeck upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Yodeck, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Yodeck Single Sign-On](#configure-yodeck-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Yodeck](#create-yodeck-test-user)**  – du har en motsvarighet för Britta Simon i Yodeck som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Yodeck:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Yodeck** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, utföra följande steg:

    ![Yodeck domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.yodeck.com/login`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Konfigurera Yodeck Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din Yodeck företagets webbplats som administratör.

2. Klicka på **användarinställningar** alternativet form det övre högra hörnet av sidan och väljer **kontoinställningar**.

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure1.png)

3. Välj **SAML** och utför följande steg:

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure2.png)

    a. Välj **importera från URL: en**.

    b. I den **URL** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat från Azure-portalen och klicka på **Import**.
    
    c. När du har importerat **Appfederationsmetadata**, återstående fält fylla i automatiskt.

    d. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yodeck.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Yodeck**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Yodeck**.

    ![Länken Yodeck i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-yodeck-test-user"></a>Skapa Yodeck testanvändare

Om du vill aktivera Azure AD-användare att logga in på Yodeck, måste de etableras i Yodeck. När det gäller Yodeck är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Yodeck företag som administratör.

2. Klicka på **användarinställningar** alternativet form det övre högra hörnet av sidan och väljer **användare**.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user1.png)

3. Klicka på **+ användare** att öppna den **användarinformation** fliken.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user2.png)

4. I dialogrutan **Användarinformation** utför du följande steg:

    ![Lägga till medarbetare](./media/yodeck-tutorial/user3.png)

    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: brittasimon@contoso.com.

    d. Välj lämplig **behörigheterna** alternativet enligt organisationens behov.
    
    e. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Yodeck i åtkomstpanelen, bör det vara loggas in automatiskt till Yodeck som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

