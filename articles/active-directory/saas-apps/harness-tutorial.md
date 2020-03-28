---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Harness | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Harness.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026958"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Harness

I den här självstudien får du lära dig hur du integrerar Utnyttja med Azure Active Directory (Azure AD). När du integrerar Utnyttja med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Harness.
* Gör att användarna automatiskt loggas in på Utnyttja med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Utnyttja en enda inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Selen stöder **SP och IDP** initierade SSO

## <a name="adding-harness-from-the-gallery"></a>Lägga till sele från galleriet

Om du vill konfigurera integreringen av Harness i Azure AD måste du lägga till Harness från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Harness** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Utnyttja** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Konfigurera och testa azure AD enkel inloggning för Harness

Konfigurera och testa Azure AD SSO med Harness med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Harness.

Om du vill konfigurera och testa Azure AD SSO med Harness slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Harness SSO](#configure-harness-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Harness-testanvändare](#create-harness-test-user)** – om du vill ha en motsvarighet till B.Simon i Harness som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På sidan Integrering av **Harness-program**på sidan **Programintegrering** i [Azure](https://portal.azure.com/)hittar du avsnittet **Hantera** och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.harness.io/`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Du kommer att få den faktiska svars-URL:en från avsnittet **Konfigurera sso-avsnitt,** som förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera selar** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Harness.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Utnyttja**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-harness-sso"></a>Konfigurera SSO

1. Om du vill automatisera konfigurationen i Harness måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup Harness** kommer att leda dig till Harness ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Harness. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Harness manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats för Harness som administratör och utför följande steg:

4. Klicka på**Autentiseringsinställningar för** **kontinuerlig säkerhet** > **åtkomsthantering** > längst upp till höger på sidan .

    ![Sele konfiguration](./media/harness-tutorial/configure01.png)

5. På **SSO Providers** avsnitt, klicka på **+ Lägg till SSO Providers** > **SAML**.

    ![Sele konfiguration](./media/harness-tutorial/configure03.png)

6. Gör följande på popup-fönstret **SAML-provider:**

    ![Sele konfiguration](./media/harness-tutorial/configure02.png)

    a. Kopiera **i din SSO-leverantör, aktivera SAML-baserad inloggning, ange sedan följande URL-instans** och klistra in den i textrutan Svara URL i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    b. Skriv visningsnamnet i textrutan **Visningsnamn.**

    c. Klicka på **Välj fil** om du vill ladda upp XML-filen federationsmetadata som du har hämtat från Azure AD.

    d. Klicka på **SKICKA**.

### <a name="create-harness-test-user"></a>Skapa seletestanvändare

Om du vill att Azure AD-användare ska kunna logga in på Harness måste de etableras i Harness. I Harness är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Harness som administratör.

1. Klicka på Användare av **kontinuerlig säkerhet** > **åtkomsthantering** > längst upp till höger på sidan **.**

    ![Sele konfiguration](./media/harness-tutorial/configure04.png)

1. Klicka på **+ Lägg till användare**till höger på sidan .

    ![Sele konfiguration](./media/harness-tutorial/configure05.png)

1. Gör följande på popup-fönstret **Lägg till användare:**

    ![Sele konfiguration](./media/harness-tutorial/configure06.png)

    a. I textrutan **E-postadress (er)** anger `B.simon@contoso.com`du e-postmeddelandet för användaren som .

    b. Välj **användargrupper**.

    c. Klicka på **Skicka**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SNESS på åtkomstpanelen ska du automatiskt loggas in på den sele som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Att utnyttja med Azure AD](https://aad.portal.azure.com/)

