---
title: 'Självstudiekurs: Azure Active Directory-integrering med ExpenseIn | Microsoft-dokument'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102847"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Självstudiekurs: Integrera ExpenseIn med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar ExpenseIn med Azure Active Directory (Azure AD). När du integrerar ExpenseIn med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ExpenseIn.
* Aktivera dina användare så att de automatiskt loggas in på ExpenseIn med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* KostnadI enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. ExpenseIn stöder **SP och IDP** initierade SSO.

## <a name="adding-expensein-from-the-gallery"></a>Lägga till ExpenseIn från galleriet

Om du vill konfigurera integreringen av ExpenseIn i Azure AD måste du lägga till ExpenseIn från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ExpenseIn** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ExpenseIn** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med ExpenseIn med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ExpenseIn.

Så här konfigurerar och testar du Azure AD SSO med ExpenseIn:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera ExpenseIn](#configure-expensein)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa ExpenseIn-testanvändare](#create-expensein-test-user)** om du vill ha en motsvarighet till B.Simon i ExpenseIn som är länkad till Azure AD-representationen för användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet **Hantera** på sidan Kostnad i programmet och välj Enkel inloggning på sidan **Utgiftsin-programintegrering.** **ExpenseIn**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande steg:

    Skriv någon av webbadressen i textrutan **Svara URL:**

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.expensein.com/saml`

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera Url för App **Federationsmetadata** och klicka på **Hämta** för att hämta **certifikatet (Base64)** och spara det på datorn.

   ![Länk för nedladdning av certifikatet](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Kopiera lämpliga webbadresser i avsnittet **Ställ in utgiftsin** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurera ExpenseIn

1. Om du vill automatisera konfigurationen i ExpenseIn måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **InstallationskostnaderI** leder dig till Programmet ExpenseIn. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på ExpenseIn. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera ExpenseIn manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen ExpenseIn som administratör och utför följande steg:

4. Klicka på **Admin** högst upp på sidan och navigera sedan till **Enkel inloggning** och klicka på Lägg **till leverantör**.

     ![KostnadI konfiguration](./media/expenseIn-tutorial/config01.png)

5. Gör följande på popup-fönstret **Ny identitetsprovider:**

    ![KostnadI konfiguration](./media/expenseIn-tutorial/config02.png)

    a. Skriv namnet som ex:Azure i textrutan **Providernamn.**

    b. Välj **Ja** som **Tillåt provider Intitated Sign-On**.

    c. Klistra in värdet **för inloggnings-URL**i textrutan **Måladress** , som du har kopierat från Azure-portalen.

    d. Klistra in värdet för **Azure AD-identifierare**i textrutan **Utfärdare** som du har kopierat från Azure-portalen.

    e. Öppna certifikatet (Base64) i Anteckningar, kopiera innehållet och klistra in det i textrutan **Certifikat.**

    f. Klicka på **Skapa**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ExpenseIn.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **ExpenseIn**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-expensein-test-user"></a>Skapa utgiftsin-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på ExpenseIn måste de etableras i ExpenseIn. I ExpenseIn är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på ExpenseIn som administratör.

2. Klicka på **Admin** högst upp på sidan och navigera sedan till **Användare** och klicka på **Ny användare**.

     ![KostnadI konfiguration](./media/expenseIn-tutorial/config03.png)

3. Gör följande på popup-fönstret **Information:**

    ![KostnadI konfiguration](./media/expenseIn-tutorial/config04.png)

    a. I textrutan **Förnamn** anger du förnamnet för användaren som **B**.

    b. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

    c. I textrutan **E-post** anger du användarens e-postadress som `B.Simon@contoso.com`.

    d. Klicka på **Skapa**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Utgiftsin på åtkomstpanelen ska du automatiskt loggas in på den UtgiftsIn som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
