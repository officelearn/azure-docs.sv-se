---
title: 'Självstudier: Azure Active Directory-integrering med ExpenseIn | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102847"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Självstudier: Integrera ExpenseIn med Azure Active Directory

I de här självstudierna lär du dig att integrera ExpenseIn med Azure Active Directory (AD Azure). När du integrerar ExpenseIn med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till ExpenseIn.
* Ge dina användare att automatiskt inloggad till ExpenseIn med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration ExpenseIn enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för ExpenseIn **SP och IDP** -initierad SSO.

## <a name="adding-expensein-from-the-gallery"></a>Att lägga till ExpenseIn från galleriet

För att konfigurera integrering av ExpenseIn i Azure AD, som du behöver lägga till ExpenseIn från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **ExpenseIn** i sökrutan.
1. Välj **ExpenseIn** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med ExpenseIn med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i ExpenseIn för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med ExpenseIn, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera ExpenseIn](#configure-expensein)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare ExpenseIn](#create-expensein-test-user)**  har en motsvarighet för B.Simon i ExpenseIn som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **ExpenseIn** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, utföra följande steg:

    I den **svars-URL** text skriver du en av URL: en:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://app.expensein.com/saml`

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klicka på **Hämta** att ladda ned den **certifikat (Base64)** och spara den på din dator.

   ![Länk för hämtning av certifikat](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. På den **konfigurera ExpenseIn** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurera ExpenseIn

1. Om du vill automatisera konfigurationen inom ExpenseIn, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet ExpenseIn** omdirigerar dig till programmet ExpenseIn. Ange administratörsautentiseringsuppgifter för att logga in på ExpenseIn därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 5.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera ExpenseIn manuellt, öppna ett nytt webbläsarfönster och logga till ExpenseIn företagets webbplatsen som administratör och utför följande steg:

4. Klicka på **Admin** överst på sidan Gå sedan till **enkel inloggning** och klicka på **Lägg till provider**.

     ![ExpenseIn konfiguration](./media/expenseIn-tutorial/config01.png)

5. På den **nya identitetsprovider** popup, utför följande steg:

    ![ExpenseIn konfiguration](./media/expenseIn-tutorial/config02.png)

    a. I den **providernamn** text skriver du namnet som ex: Azure.

    b. Välj **Ja** som **Tillåt providern Intitated inloggning**.

    c. I den **målwebbadressen** text rutan, klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    d. I den **utfärdare** text rutan, klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    e. Öppna certifikat (Base64) i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    f. Klicka på **Skapa**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ExpenseIn.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **ExpenseIn**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-expensein-test-user"></a>Skapa ExpenseIn testanvändare

Om du vill aktivera Azure AD-användare att logga in på ExpenseIn, måste de etableras i ExpenseIn. I ExpenseIn är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på ExpenseIn som administratör.

2. Klicka på **Admin** överst på sidan Gå sedan till **användare** och klicka på **ny användare**.

     ![ExpenseIn konfiguration](./media/expenseIn-tutorial/config03.png)

3. På den **information** popup, utför följande steg:

    ![ExpenseIn konfiguration](./media/expenseIn-tutorial/config04.png)

    a. I **Förnamn** text, ange först namnet på användaren som **B**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

    c. I **e-post** text, ange den e-postadressen för användaren som `B.Simon@contoso.com`.

    d. Klicka på **Skapa**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen ExpenseIn i åtkomstpanelen, bör det vara loggas in automatiskt till ExpenseIn som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
