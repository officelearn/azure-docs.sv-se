---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SuccessFactors | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292987"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SuccessFactors

I den här självstudien får du lära dig hur du integrerar SuccessFactors med Azure Active Directory (Azure AD). När du integrerar SuccessFactors med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SuccessFactors.
* Gör att användarna automatiskt loggas in på SuccessFactors med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SuccessFactors enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SuccessFactors **SP** stöder SP-initierad SSO.
* När du har konfigurerat SuccessFactors kan du framtvinga sessionskontroller, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Lägga till SuccessFactors från galleriet

För att konfigurera integreringen av SuccessFactors till Azure AD behöver du lägga till SuccessFactors från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SuccessFactors** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SuccessFactors** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurera och testa Azure AD SSO för SuccessFactors

Konfigurera och testa Azure AD SSO med SuccessFactors med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SuccessFactors.

Så här konfigurerar och testar du Azure AD SSO med SuccessFactors:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera SuccessFactors SSO](#configure-successfactors-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa SuccessFactors-testanvändare](#create-successfactors-test-user)** – om du vill ha en motsvarighet till B.Simon i SuccessFactors som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **SuccessFactors-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för SuccessFactors-klienten](https://www.successfactors.com/content/ssf-site/en/support.html).

4. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser i avsnittet **Konfigurera SuccessFactors** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SuccessFactors.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **SuccessFactors**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-successfactors-sso"></a>Konfigurera SuccessFactors SSO

1. I ett annat webbläsarfönster loggar du in på din **SuccessFactors-administratörsportal** som administratör.

2. Gå till **Application Security** (Programsäkerhet) och sedan till **funktionen för enkel inloggning**.

3. Placera valfritt värde i **Reset Token** (Återställ token) och klicka på **Save Token** (Spara Token) för att aktivera enkel inloggning med SAML.

    ![Konfigurera enkel inloggning på appsidan][11]

    > [!NOTE]
    > Det här värdet används som på/av-växel. Om ett värde sparas är enkel inloggning med SAML PÅ. Om ett tomt värde sparas är enkel inloggning med SAML AV.

4. Gå till skärmbilden nedan och utför följande åtgärder:

    ![Konfigurera enkel inloggning på appsidan][12]
  
    a. Markera alternativknappen **SAML v2 SSO** (Enkel inloggning med SAML v2)
  
    b. Ange **SAML Asserting Party Name** (Namn på försäkrande part för SAML) (till exempel SAML-utfärdare plus företagsnamn).

    c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    d. Välj **Assertion** (Försäkran) för **Require Mandatory Signature** (Kräv obligatorisk signatur).

    e. Välj **Enabled** (Aktiverad) för **Enable SAML Flag** (Aktivera SAML-flagga).

    f. Välj **No** (Nej) för **Login Request Signature(SF Generated/SP/RP)** (Signatur för inloggningsbegäran (SF-genererad/SP/RP)).

    g. Välj **Browser/Post Profile** (Webbläsar-/postprofil) för **SAML Profile** (SAML-profil).

    h. Välj **No** (Nej) för **Enforce Certificate Valid Period** (Tillämpa giltighetsperiod för certifikat).

    i. Kopiera innehållet i den nedladdade certifikatfilen från Azure-portalen och klistra in det i textrutan **SAML Verifying Certificate** (Verifieringscertifikat för SAML).

    > [!NOTE] 
    > Certifikatinnehållet måste ha starttaggar och sluttaggar för certifikat.

5. Gå till SAML-V2 och utför sedan följande steg:

    ![Konfigurera enkel inloggning på appsidan][13]

    a. Välj **Yes** (Ja) för **Support SP-initiated Global Logout** (Stöd SP-initierad global utloggning).

    b. I textrutan **Global Logout Service URL (LogoutRequest destination)** (Tjänst-URL för global utloggning (LogoutRequest-destination)) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Välj **No** (Nej) för **Require sp must encrypt all NameID element** (Kräv att SP krypterar alla NameID-element).

    d. Välj **unspecified** (ospecificerad) för **NameID Format** (NameID-format).

    e. Välj **Ja** för **Enable sp initiated login (AuthnRequest)** (Aktivera SP-initierad inloggning (AuthnRequest)).

    f. I textrutan **Send request as Company-Wide issuer** (Skicka begäran som företagsomfattande utfärdare) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

6. Utför de här stegen om du göra användarnamnen för inloggning skiftlägeskänsliga.

    ![Konfigurera enkel inloggning][29]

    a. Gå till **Company Settings** (Företagsinställningar) (nästan längst ned).

    b. Markera kryssrutan nära **Enable Non-Case-Sensitive Username** (Aktivera ej skiftlägeskänsligt användarnamn).

    c. Klicka på **Spara**.

    > [!NOTE]
    > Om du försöker aktivera detta så kontrollerar systemet om det skapa ett duplicerat SAML-inloggningsnamn. Ett exempel kan vara om kunden har användarnamnen User1 och user1. Om skiftlägeskänslighet tas bort blir dessa namn dubbletter. Systemet visar ett felmeddelande och aktiverar inte funktionen. Kunden måste ändra något av användarnamnen så att de stavas olika.

### <a name="create-successfactors-test-user"></a>Skapa SuccessFactors-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på SuccessFactors måste de etableras i SuccessFactors. När det gäller SuccessFactors är etablering en manuell aktivitet.

För att skapa användare i SuccessFactors behöver du kontakta [SuccessFactors-supportteamet](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SuccessFactors-panelen i åtkomstpanelen bör du automatiskt loggas in på SuccessFactors som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SuccessFactors med Azure AD](https://aad.portal.azure.com)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du SuccessFactors med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
