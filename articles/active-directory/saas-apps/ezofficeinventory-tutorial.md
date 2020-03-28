---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med EZOfficeInventory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EZOfficeInventory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd277eb3588743e7fb864445d4c6fc8397507b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77370451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med EZOfficeInventory

I den här självstudien får du lära dig hur du integrerar EZOfficeInventory med Azure Active Directory (Azure AD). När du integrerar EZOfficeInventory med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EZOfficeInventory.
* Gör att användarna automatiskt loggas in på EZOfficeInventory med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* EZOfficeInventory enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* EZOfficeInventory stöder **SP** initierade SSO
* EZOfficeInventory stöder just in time-användaretablering **Just In Time**
* När du har konfigurerat EZOfficeInventory kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Lägga till EZOfficeInventory från galleriet

Om du vill konfigurera integreringen av EZOfficeInventory i Azure AD måste du lägga till EZOfficeInventory från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **EZOfficeInventory** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **EZOfficeInventory** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Konfigurera och testa en azure AD-inloggning för EZOfficeInventory

Konfigurera och testa Azure AD SSO med EZOfficeInventory med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i EZOfficeInventory.

Så här konfigurerar och testar du Azure AD SSO med EZOfficeInventory:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera EZOfficeInventory SSO](#configure-ezofficeinventory-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa EZOfficeInventory testanvändare](#create-ezofficeinventory-test-user)** - att ha en motsvarighet till B.Simon i EZOfficeInventory som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj enkel inloggning på sidan **EZOfficeInventory-programintegration** . **Manage** **single sign-on**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EZOfficeInventory Client support team](mailto:support@ezofficeinventory.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EZOfficeInventory-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan, EZOfficeInventory ansökan förväntar sig några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera EZOfficeInventory.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EZOfficeInventory.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **EZOfficeInventory**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-ezofficeinventory-sso"></a>Konfigurera EZOfficeInventory SSO

1. Om du vill automatisera konfigurationen i EZOfficeInventory måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Konfigurera EZOfficeInventory** kommer att leda dig till EZOfficeInventory ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på EZOfficeInventory. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera EZOfficeInventory manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets EZOfficeInventory-företagswebbplats som administratör och utför följande steg:

1. Klicka på **Profil** längst upp till höger på sidan och navigera sedan till **Inställningar** > **Lägg till Ons**.

    ![EZOfficeInventory konfiguration](./media/ezofficeinventory-tutorial/configure01.png)

1. Bläddra ned till **avsnittet SAML-integrering** och utför följande steg:

    ![EZOfficeInventory konfiguration](./media/ezofficeinventory-tutorial/configure02.png)

    a. Markera alternativet **Aktiverad.**

    b. I textrutan **URL för identitetsprovider** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    c. Öppna Base64-kodade certifikatet i anteckningar, kopiera innehållet och klistra in det i textrutan **Identitetsprovidercertifikat.**

    d. I textrutan **Logga in Text** text anger du texten på inloggningsknappen.

    e. Skriv **first_name i**textrutan **Förnamn** .

    f. Skriv **last_name**i textrutan **Efternamn** .

    g. Skriv **e-post**i textrutan **E-post** .

    h. Välj din roll enligt dina krav från **alternativet EZOfficeInventory Role By Default.**

    i. Klicka på **Uppdatera**.

### <a name="create-ezofficeinventory-test-user"></a>Skapa EZOfficeInventory testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i EZOfficeInventory. EZOfficeInventory stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i EZOfficeInventory skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen EZOfficeInventory på åtkomstpanelen bör du automatiskt loggas in på den EZOfficeInventory som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Prova EZOfficeInventory med Azure AD](https://aad.portal.azure.com/)