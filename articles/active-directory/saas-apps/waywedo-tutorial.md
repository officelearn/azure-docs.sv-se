---
title: 'Självstudiekurs: Azure Active Directory-integrering med Way We Do | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67310406"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Självstudiekurs: Integrera hur vi gör med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar sätt vi gör med Azure Active Directory (Azure AD). När du integrerar Way We Do med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Way We Do.
* Gör det möjligt för användarna att automatiskt loggas in på Way We Do med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Way We Do single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Way We Do stöder **SP** initierade SSO
* Way We Do stöder **just in time-användares** etablering

## <a name="adding-way-we-do-from-the-gallery"></a>Lägga till sätt vi gör från galleriet

Om du vill konfigurera integreringen av Way We Do i Azure AD måste du lägga till sätt vi gör från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Way We Do** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Sätt vi gör** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Way We Do med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren på ett sätt vi gör.

Om du vill konfigurera och testa Azure AD SSO med Way We Do slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Way We Do SSO](#configure-way-we-do-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa sätt vi gör-testanvändare](#create-way-we-do-test-user)** - att ha en motsvarighet till Britta Simon på ett sätt vi gör som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan Way We **Do-programintegration**och väljer Enkel inloggning . **Way We Do** **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Way We Do Client support team](mailto:support@waywedo.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser på avsnittet **Konfigurera sätt att göra** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurera sätt vi gör SSO

1. Om du vill automatisera konfigurationen i Way We Do måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. Efter att ha lagt till förlängning i webbläsaren, klicka på **Setup Way Vi gör** kommer att hänvisa dig till hur vi gör ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Way We Do. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Way We Do manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Way We Do som administratör och utför följande steg:

1. Klicka på **personikonen** längst upp till höger på en sida i Way We Do och klicka sedan på **Konto** i rullgardinsmenyn.

    ![Sätt vi gör konto](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klicka på **menyikonen** för att öppna menyn push-navigering och Klicka **på Enkel inloggning**.

    ![Sätt vi gör singel](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Gör följande på sidan **Enkel inloggning:**

    ![Sätt vi sparar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klicka **på** växla med enkel inloggning till **Ja** för att aktivera Enkel inloggning.

    b. Ange ditt namn i textrutan **För en inloggningsnamn.**

    c. Klistra in värdet för **Azure AD-identifierare**i textrutan **för entitets-ID** som du har kopierat från Azure-portalen.

    d. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    e. Ladda upp certifikatet genom att klicka på **välj-knappen** **bredvid Certifikat**.

    f. **Valfria inställningar** -
    
    * Aktivera lösenord - När det här alternativet är inaktiverat fungerar det vanliga lösenordet för Way We Do så att användarna bara kan använda enkel inloggning.

    * Aktivera automatisk etablering – När detta är aktiverat jämförs den e-postadress som används för inloggning automatiskt med listan över användare på ett sätt vi gör. Om e-postadressen inte matchar en aktiv användare på way we do, läggs automatiskt ett nytt användarkonto för den person som loggar in och begär eventuell information som saknas.

      > [!NOTE]
      > Användare som läggs till via enkel inloggning läggs till som allmänna användare och tilldelas inte en roll i systemet. En administratör kan gå in och ändra sin säkerhetsroll som redigerare eller administratör och kan även tilldela en eller flera roller i organisationsschemat.

    g. Klicka på **Spara** om du vill spara inställningarna.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Way We Do.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Way We Do**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-way-we-do-test-user"></a>Skapa sätt vi gör testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Way We Do. Way We Do stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns på Way We Do skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [Supportteamet för Way We Do Client](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Sätt vi gör på åtkomstpanelen ska du automatiskt loggas in på det sätt vi gör som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)