---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Workplace by Facebook | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de84f2aee5f59d14ab70cb1687968643c4cdb31e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Workplace by Facebook

I den här självstudien får du lära dig hur du integrerar Workplace by Facebook med Azure Active Directory (Azure AD). När du integrerar Workplace by Facebook med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Workplace av Facebook.
* Gör det möjligt för användarna att automatiskt loggas in på Workplace av Facebook med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En prenumeration på arbetsplatsen av Facebooks enkel inloggning (SSO).

> [!NOTE]
> Facebook har två produkter, Workplace Standard (kostnadsfri) och Workplace Premium (betalprodukt). Alla Workplace Premium-klientorganisationer kan konfigurera integrering med SCIM och enkel inloggning utan att det påverkar kostnaden eller de licenser som behövs. Enkel inloggning och SCIM är inte tillgängliga i Workplace Standard-instanser.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Workplace by Facebook har stöd för **SP**-initierad enkel inloggning
* Workplace by Facebook har stöd för **just-in-time-etablering**
* Workplace by Facebook har stöd för **[automatisk användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)**
* Workplace by Facebook Mobile-programmet kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.
* När du har konfigurerat Workplace by Facebook kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Lägga till Workplace by Facebook från galleriet

För att konfigurera integrering av Workplace by Facebook med Azure AD behöver du lägga till Workplace by Facebook från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Arbetsplats av Facebook** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Arbetsplats av Facebook** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Konfigurera och testa Azure AD SSO for Workplace by Facebook

Konfigurera och testa Azure AD SSO med Workplace by Facebook med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren på Workplace by Facebook.

Så här konfigurerar och testar du Azure AD SSO med Workplace by Facebook:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    * **[Skapa Workplace av Facebook-testanvändare](#create-workplace-by-facebook-test-user)** – om du vill ha en motsvarighet till B.Simon på arbetsplatsen av Facebook som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/), på sidan **Integrering av Programmet Arbetsplats efter Facebook,** hittar du avsnittet **Hantera** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instancename>.facebook.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.facebook.com/company/<instanceID>`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Se sidan Autentisering på instrumentpanelen för workplace company för rätt värden för din workplace-community, förklaras detta senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera arbetsplats efter Facebook** kopierar du lämpliga webbadresser baserat på dina krav.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Workplace by Facebook.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Arbetsplats av **Facebook**i listan över applikationer .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurera Arbetsplats av Facebook SSO

1. Om du vill automatisera konfigurationen på Workplace by Facebook måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren klickar du på **Konfigurera Arbetsplats av Facebook** leder dig till workplace by Facebook-applikationen. Därifrån anger du administratörsuppgifter för att logga in på Workplace by Facebook. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Workplace by Facebook manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Workplace by Facebook som administratör och utför följande steg:

    > [!NOTE]
    > Som en del av SAML-autentiseringsprocessen kan Workplace by Facebook utnyttja frågesträngar på upp till 2,5 kB i storlek för att skicka parametrar till Azure AD.

1. Navigera till fliken > **Säkerhetsautentisering** på den vänstra navigeringspanelen. **Security**

    ![Administrationspanel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Markera alternativet **SSO(Single-sign on).**
    
    b. Klicka på **+Lägg till ny SSO-leverantör**.
    > [!NOTE]
    > Kontrollera också kryssrutan Lösenordsinloggning. Administratörer kan behöva det här alternativet för inloggning när du gör certifikatet rollover för att stoppa sig få utelåst.

1. Under fliken **Autentisering** väljer du **Enkel inloggning** och utför följande steg:

    ![Fliken Autentisering](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. I **namnet på SSO-providern**anger du SSO-förekomstnamnet som Azureadsso.

    b. I textrutan **SAML URL** klistrar du in det värde för **Login URL** (Inloggnings-URL) som du har kopierat från Azure-portalen.

    c. I **SMS-textrutan för SAML Issuer** klistrar du in värdet för **Azure AD-identifierare**som du har kopierat från Azure-portalen.

    d. Öppna ditt **base-64-kodade certifikat** som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet i Urklipp och klistra sedan in den i textrutan **SAML Certificate** (SAML-certifikat).

    e. Kopiera **målgrupps-URL:en** för din instans och klistra in den i textrutan **Identifierar (Entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    f. Kopiera **mottagarens URL** för din instans och klistra in den i **Textrutan Logga in på URL** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    g. Kopiera **ACS-url:en (Assertion Consumer Service)** för din instans och klistra in den i textrutan **Svara på URL** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    h. Rulla ned till slutet av avsnittet och klicka på knappen **Test SSO** (Testa enkel inloggning). Då visas ett popup-fönster med Azure AD-inloggningssidan. Ange dina autentiseringsuppgifter som vanligt för att autentisera.

    **Felsökning:** Kontrollera att e-postadressen som returneras tillbaka från Azure AD är samma som det Workplace-konto som du är inloggad med.

    i. När testet är klart rullar du längst ned på sidan och klickar på knappen **Spara**.

    j. Alla användare som använder Workplace ser nu Azure AD-inloggningssidan för autentisering.

1. **OMdirigering av SAML-utloggning (valfritt)** -

    Du kan även välja att konfigurera en SAML-utloggnings-URL som kan användas för att peka på utloggningssidan för Azure AD. När den här inställningen aktiveras och konfigureras omdirigeras användarna inte längre till utloggningssidan för Workplace. I stället omdirigeras de till den URL som lades till i inställningen för SAML-utloggningsomdirigering.

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentiseringsfrekvens

Du kan konfigurera Workplace att fråga efter en SAML-kontroll varje dag, var tredje dag, varje vecka, varannan vecka, varje månad eller aldrig.

> [!NOTE]
> Minimivärdet för SAML-kontrollen i mobilprogram är inställt på en vecka.

Du kan också tvinga fram en SAML-återställning för alla användare som använder knappen: Kräv SAML-autentisering för alla användare nu.

### <a name="create-workplace-by-facebook-test-user"></a>Skapa Workplace by Facebook-testanvändare

I det här avsnittet skapas en användare som heter B.Simon på Workplace by Facebook. Workplace by Facebook har stöd för just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd för dig i det här avsnittet. Om det inte finns någon användare i Workplace by Facebook skapas en ny när du försöker få åtkomst till Workplace by Facebook.

>[!Note]
>Kontakta [Workplace by Facebook-kundsupporten](https://workplace.fb.com/faq/) om du behöver skapa en användare manuellt

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Workplace by Facebook-panelen i åtkomstpanelen bör du automatiskt loggas in på Workplace by Facebook som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testa SSO för arbetsplats av Facebook (mobil)

1. Öppna Workplace av Facebook Mobile-applikation. På inloggningssidan klickar du på **LOGGA IN**.

    ![Inloggningen](./media/workplacebyfacebook-tutorial/test05.png)

2. Ange företagets e-postadress och klicka på **FORTSÄTT.**

    ![E-postmeddelandet](./media/workplacebyfacebook-tutorial/test02.png)

3. Klicka **bara en gång**.

    ![Den en gång](./media/workplacebyfacebook-tutorial/test04.png)

4. Klicka på **Tillåt**.

    ![Tillåt](./media/workplacebyfacebook-tutorial/test03.png)

5. Slutligen efter lyckad inloggning kommer programmets hemsida att visas.    

    ![Hemsidan](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera etablering av användare](workplacebyfacebook-provisioning-tutorial.md)

- [Prova Arbetsplats av Facebook med Azure AD](https://aad.portal.azure.com)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
