---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Workday | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f39b6f58b250d68a3b2ce962f158c7df36d812
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Workday

I den här självstudien får du lära dig hur du integrerar Workday med Azure Active Directory (Azure AD). När du integrerar Workday med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Workday.
* Gör att användarna automatiskt loggas in på Workday med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En automatisk inloggning (SSO) aktiverad prenumeration (Workday single sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Workday stöder **SP** initierade SSO.

* När du har konfigurerat Workday kan du framtvinga Sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Lägga till arbetsdag från galleriet

Om du vill konfigurera integreringen av Workday i Azure AD måste du lägga till Workday från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Arbetsdag i sökrutan i avsnittet **Lägg till från galleriet.** **Workday**
1. Välj **Arbetsdag** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Konfigurera och testa en azure AD-inloggning för arbetsdag

Konfigurera och testa Azure AD SSO med Workday med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Workday.

Om du vill konfigurera och testa Azure AD SSO med Workday slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
2. **[Konfigurera Workday](#configure-workday)** för att konfigurera SSO-inställningarna på programsidan.
    1. **[Skapa workday-testanvändare](#create-workday-test-user)** för att ha en motsvarighet till B.Simon i Workday som är länkad till Azure AD-representationen för användaren.
3. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Arbetsdagsprogram** på sidan **Programintegrering** på [Azure](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Skriv en URL med följande mönster i textrutan **Identifierare:**`http://www.workday.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och svars-URL:en. Din svars-URL måste ha en underdomän till exempel: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Använda något `http://www.myworkday.com` liknande `http://myworkday.com` fungerar men inte. Kontakta Supportteamet för [Workday Client](https://www.workday.com/en-us/partners-services/services/support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Ditt Workday-program förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Workday ansökan förväntar **namnidentifierare** som ska mappas med **user.mail**, **UPN**, etc., så du måste redigera attributet mappning genom att klicka på **Redigera** ikonen och ändra attributet mappning.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (user.userprincipalname) som standard. Du måste mappa namn-ID med faktiska användar-ID i ditt Workday-konto (din e-post, UPN, etc.) för att framgångsrikt fungera sso.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Om du vill ändra **signeringsalternativen** enligt dina krav klickar du på **Knappen Redigera** för att öppna dialogrutan **SAML-signeringscertifikat.**

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Välj **Signera SAML-svar och påstående** för **signeringsalternativ**.

    b. Klicka på **Spara**

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera arbetsdag** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Arbetsdag**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-workday"></a>Konfigurera arbetsdag

1. Logga in på webbplatsen Workday som administratör i ett annat webbläsarfönster.

2. Sök i **sökrutan** med namnet **Redigera klientinstallation – Säkerhet** längst upp till vänster på startsidan.

    ![Redigera klientsäkerhet](./media/workday-tutorial/IC782925.png "Redigera klientsäkerhet")

3. Gör följande i avsnittet **Omdirigeringsadresser:**

    ![Url:er för omdirigering](./media/workday-tutorial/IC7829581.png "Url:er för omdirigering")

    a. Klicka på **Lägg till rad**.

    b. I **url:en för inloggningsomdirigering,** **timeoutomdirigering** och **textrutan Url för mobileromdirigering** klistrar du in **inloggnings-URL:en** som du har kopierat från avsnittet **Konfigurera arbetsdag i** Azure-portalen.

    c. I **textrutan URL för utrinsträckningsomen för utloggning** klistrar du in **URL:en för utloggning** som du har kopierat från avsnittet **Konfigurera arbetsdag** i Azure-portalen.

    d. Markera miljönamnet i Textrutan **Används för miljöer.**  

   > [!NOTE]
   > Värdet för attributet Miljö är kopplat till värdet för klient-URL:en:  
   > -Om domännamnet för url:en för Arbetsdagsklient börjar med impl till exempel: * https://www.myworkday.com/"klient"/login-saml2.htmld*) måste attributet **Miljö** anges till Implementering.  
   > -Om domännamnet börjar med något annat, måste du kontakta [Workday Client supportteam](https://www.workday.com/en-us/partners-services/services/support.html) för att få matchande **miljövärde.**

4. Gör följande i avsnittet **SIML-inställningar:**

    ![INSTALLATION AV SAML](./media/workday-tutorial/IC782926.png "INSTALLATION AV SAML")

    a.  Välj **Aktivera SAML-autentisering**.

    b.  Klicka på **Lägg till rad**.

5. I avsnittet **SAML Identity Providers** utför du följande steg:

    ![SAML-identitetsprovider](./media/workday-tutorial/IC7829271.png "SAML-identitetsprovider")

    a. Skriv ett providernamn i textrutan **Identitetsprovidernamn** (till exempel: *SPInitiatedSSO*).

    b. Kopiera azure **AD-identifierare** i avsnittet **Konfigurera arbetsdag** i Azure-portalen och klistra in det sedan i textrutan **Utfärdare.**

    ![SAML-identitetsprovider](./media/workday-tutorial/IC7829272.png "SAML-identitetsprovider")

    c. Kopiera **url-värdet för utloggning** i Azure-portalen **Set up Workday** och klistra sedan in det i textrutan **Logout Response URL.**

    d. Kopiera **värdet** **för inloggnings-URL:en** i Azure-portalen och klistra in det sedan i textrutan **för IdP SSO-tjänstens URL.**

    e. Markera miljönamnet i Textrutan **Används för miljöer.**

    f. Klicka på **Offentligt nyckelcertifikat för identitetsprovider**och sedan på **Skapa**.

    ![Skapa](./media/workday-tutorial/IC782928.png "Skapa")

    g. Klicka på **Skapa x509 Public Key**.

    ![Skapa](./media/workday-tutorial/IC782929.png "Skapa")

6. Gör följande i avsnittet **Visa x509 offentlig nyckel:**

    ![Visa den offentliga nyckeln x509](./media/workday-tutorial/IC782930.png "Visa den offentliga nyckeln x509")

    a. Skriv ett namn på certifikatet (till exempel *PPE\_SP*) i textrutan **Namn** .

    b. Skriv certifikatets giltiga från-attributvärde i textrutan **Giltig** från.

    c.  Skriv det giltiga till-attributvärdet för certifikatet i textrutan **Giltig** till.

    > [!NOTE]
    > Du kan få det giltiga från-datumet och det giltiga till-datumet från det nedladdade certifikatet genom att dubbelklicka på det.  Datumen visas under fliken **Information.**
    >
    >

    d.  Öppna ditt bas-64-kodade certifikat i anteckningar och kopiera sedan innehållet i det.

    e.  Klistra in innehållet i Urklipp i textrutan **certifikat.**

    f.  Klicka på **OK**.

7. Utför följande steg:

    ![SSO-konfiguration](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguration")

    a.  Skriv **http://www.workday.com**i textrutan **Tjänsteprovider-ID** .

    b. Välj **Töm inte SP-initierad autentiseringsbegäran**.

    c. Som **signaturmetod för autentiseringsbegäran**väljer du **SHA256**.

    ![Signaturmetod för autentiseringsbegäran](./media/workday-tutorial/WorkdaySSOConfiguration.png "Signaturmetod för autentiseringsbegäran")

    d. Klicka på **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Se till att du ställer in enkel inloggning på rätt sätt. Om du aktiverar enkel inloggning med felaktig inställning kanske du inte kan ange programmet med dina autentiseringsuppgifter och bli utelåst. I det här fallet ger Workday en backup inloggningsadress där användare kan logga in med sitt vanliga användarnamn och lösenord i följande format:[Din arbetsdag URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Skapa workday-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Workday. Arbeta med Supportteamet för [Workday Client](https://www.workday.com/partners-services/services/support.html) för att lägga till användarna i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen Arbetsdag på åtkomstpanelen bör du automatiskt loggas in på den arbetsdag som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Workday med Azure AD](https://aad.portal.azure.com)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Workday med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-workday)