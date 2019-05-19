---
title: 'Självstudier: Azure Active Directory-integrering med Bugsnag | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bugsnag.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f08b8a14-ef69-42a7-ac9d-da95f2e36287
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: jeedes
ms.openlocfilehash: 01877e4d982433608dd10fb44f2506b8bf71aff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65858926"
---
# <a name="tutorial-azure-active-directory-integration-with-bugsnag"></a>Självstudier: Azure Active Directory-integrering med Bugsnag

I den här självstudien lär du dig att integrera Bugsnag med Azure Active Directory (AD Azure).
Integreringen av Bugsnag med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Bugsnag.
* Du kan göra så att dina användare loggas in automatiskt på Bugsnag (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Bugsnag behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Bugsnag-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cloud Shell har stöd för **SP- och IDP**-initierad enkel inloggning
* Cloud Shell har stöd för **just-in-time**-användaretablering

## <a name="adding-bugsnag-from-the-gallery"></a>Lägga till Bugsnag från galleriet

För att konfigurera integreringen av Bugsnag i Azure AD måste du lägga till Bugsnag från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bugsnag från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Bugsnag**, väljer **Bugsnag** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Bugsnag i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Bugsnag baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Bugsnag upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Bugsnag behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Bugsnag](#configure-bugsnag-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Bugsnag-testanvändare](#create-bugsnag-test-user)** – för att ha en motsvarighet för Britta Simon i Bugsnag som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Bugsnag:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Bugsnag** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Bugsnag-domän och information om URL:er för enkel inloggning](common/both-replyurl.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://app.bugsnag.com/user/sign_in/saml/<org_slug>/acs`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Kontakta [kundsupporten för Bugsnag](mailto:support@bugsnag.com) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Bugsnag-domän och information om URL:er för enkel inloggning](common/both-signonurl.png)

    I rutan **Inloggnings-URL** skriver du: `https://app.bugsnag.com/user/identity_provider`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-bugsnag-single-sign-on"></a>Konfigurera enkel inloggning för Bugsnag

För att konfigurera enkel inloggning på **Bugsnag**-sidan behöver du skicka **URL:en med appfederationsmetadata** till [supportteamet för Bugsnag](mailto:support@bugsnag.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Bugsnag.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Bugsnag**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Bugsnag**.

    ![Länken för Bugsnag i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bugsnag-test-user"></a>Skapa Bugsnag-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Bugsnag. Bugsnag har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Bugsnag skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Bugsnag-panelen i åtkomstpanelen bör du automatiskt loggas in på Bugsnag som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)