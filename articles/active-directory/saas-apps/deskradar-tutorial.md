---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Deskradar | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ccb0f63476db733749c2b9bd9c297e29fe6fba6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75608575"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-deskradar"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Deskradar

I den här självstudien får du lära dig hur du integrerar Deskradar med Azure Active Directory (Azure AD). När du integrerar Deskradar med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Deskradar.
* Gör att användarna automatiskt loggas in på Deskradar med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Skrivbordsradar enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.



* Deskradar stöder **IDP**-initierad enkel inloggning



## <a name="adding-deskradar-from-the-gallery"></a>Lägga till Deskradar från galleriet

För att konfigurera integreringen av Deskradar till Azure AD behöver du lägga till Deskradar från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Deskradar** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Deskradar** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-deskradar"></a>Konfigurera och testa en azure AD-inloggning för Deskradar

Konfigurera och testa Azure AD SSO med Deskradar med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Deskradar.

Om du vill konfigurera och testa Azure AD SSO med Deskradar slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Deskradar SSO](#configure-deskradar-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Deskradar-testanvändare](#create-deskradar-test-user)** – om du vill ha en motsvarighet till B.Simon i Deskradar som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Deskradar** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<YOURDOMAIN>.deskradar.cloud`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Ersätt **YOURDOMAIN** med din Deskradar-instansdomän. Hämta dessa värden genom att kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Deskradar-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Deskradar ansökan få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.userprincipalname |
    | | |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Deskradar** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Deskradar.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Deskradar**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="configure-deskradar-sso"></a>Konfigurera Deskradar SSO

1. Om du vill automatisera konfigurationen i Deskradar måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Setup Deskradar** kommer att hänvisa dig till Deskradar ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Deskradar. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Deskradar manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Deskradar-företagswebbplats som administratör och utför följande steg:

1. Öppna panelen **Team** genom att klicka på ikonen i sidofältet.

1. Växla till fliken **Autentisering**.

1. På fliken **SAML 2.0** utför du följande steg:

    ![Deskradar-konfiguration](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Aktivera **SAML**-autentiseringsmetoden.

    b. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) anger du värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) anger du värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

1. Öppna den nedladdade **Certifikat (Base64)**-filen med ett redigeringsprogram och kopiera och klistra in innehållet i fältet **Offentligt certifikat** i Deskradar.

    ![Deskradar-konfiguration](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-deskradar-test-user"></a>Skapa Deskradar-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Deskradar. Arbeta med [Deskradar Client supportteam](mailto:support@deskradar.com) för att lägga till användarna i Deskradar-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Deskradar-panelen i åtkomstpanelen bör du automatiskt loggas in på Deskradar som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Deskradar med Azure AD](https://aad.portal.azure.com/)

