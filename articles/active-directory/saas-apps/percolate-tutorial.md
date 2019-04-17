---
title: 'Självstudier: Azure Active Directory-integrering med Percolate | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2110b1ec7d5d6b317341855ff19acf7975733e71
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617877"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Självstudier: Azure Active Directory-integrering med Percolate

I den här självstudien får du lära dig hur du integrerar Percolate med Azure Active Directory (AD Azure).
Integrera Percolate med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Percolate.
* Du kan aktivera användarna att vara automatiskt inloggad till Percolate (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Percolate, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Percolate enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Percolate stöder **SP** och **IDP** -initierad SSO

## <a name="adding-percolate-from-the-gallery"></a>Att lägga till Percolate från galleriet

För att konfigurera integrering av Percolate i Azure AD, som du behöver lägga till Percolate från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Percolate från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Percolate**väljer **Percolate** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Percolate i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Percolate baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Percolate upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Percolate, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Percolate enkel inloggning](#configure-percolate-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Percolate](#create-percolate-test-user)**  – du har en motsvarighet för Britta Simon i Percolate som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Percolate:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Percolate** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierad läge som användaren inte behöver utföra några steg som appen är redan förintegrerade med Azure.

    ![Percolate domän och URL: er enkel inloggning för information](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Percolate domän och URL: er enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://percolate.com/app/login`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

7. På den **konfigurera Percolate** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-percolate-single-sign-on"></a>Konfigurera Percolate enkel inloggning

1. I ett annat webbläsarfönster, loggar du in Percolate som administratör.

2. Till vänster på startsidan klickar du på **inställningar**.
    
    ![Konfigurera enkel inloggning](./media/percolate-tutorial/configure01.png)

3. Från vänster sida av menyraden klickar du på **SSO** under **organisation**.

    ![Konfigurera enkel inloggning](./media/percolate-tutorial/configure02.png)

    a. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I den **entitets-ID** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **x509 certifikat** box.

    d. I den **e-attributet** textrutan typ **e-postadress**.

    e. **Metadata-URL för identitetsprovider** fältet är valfritt och om du har **appfederationsmetadata** som du har kopierat från Azure-portalen, klistra in den i den **metadata-URL för identitetsprovider** textrutan.

    f. Välj **nr** som **bör AuthNRequests signeras?**.

    g. Välj **nr** som **aktivera SSO Automatisk etablering**.

    h. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Percolate.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Percolate**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Percolate**.

    ![Länken Percolate i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-percolate-test-user"></a>Skapa Percolate testanvändare

Om du vill aktivera Azure AD-användare att logga in på Percolate, måste de etableras i Percolate. I Percolate är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Percolate som administratör.

2. Från vänster sida av menyraden klickar du på **användare** under **organisation** och gå till **nya användare**.

    ![Konfigurera enkel inloggning](./media/percolate-tutorial/configure03.png)

3. På den **skapa användare** utför följande steg:

    ![Konfigurera enkel inloggning](./media/percolate-tutorial/configure04.png)

    a. I **e-post** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Fullname** text, ange namnet på användaren som **Brittasimon**.

    c. Klicka på **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Percolate i åtkomstpanelen, bör det vara loggas in automatiskt till Percolate som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

