---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ScaleX Enterprise | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594266"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ScaleX Enterprise

I den här självstudien får du lära dig hur du integrerar ScaleX Enterprise med Azure Active Directory (Azure AD). När du integrerar ScaleX Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ScaleX Enterprise.
* Gör att användarna automatiskt loggas in i ScaleX Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* ScaleX Enterprise enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* ScaleX Enterprise stöder **SP och IDP** initierad SSO

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Lägga till ScaleX Enterprise från galleriet

Om du vill konfigurera integreringen av ScaleX Enterprise i Azure AD måste du lägga till ScaleX Enterprise från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ScaleX Enterprise** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ScaleX Enterprise** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Konfigurera och testa en enda Azure AD-inloggning för ScaleX Enterprise

Konfigurera och testa Azure AD SSO med ScaleX Enterprise med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ScaleX Enterprise.

Så här konfigurerar och testar du Azure AD SSO med ScaleX Enterprise:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera ScaleX Enterprise SSO](#configure-scalex-enterprise-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa ScaleX Enterprise-testanvändare](#create-scalex-enterprise-test-user)** – om du vill ha en motsvarighet till B.Simon i ScaleX Enterprise som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **ScaleX Enterprise-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://platform.rescale.com/saml2/<company id>/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://platform.rescale.com/saml2/<company id>/acs/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [ScaleX Enterprise Client supportteam](https://info.rescale.com/contact_sales) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ditt ScaleX Enterprise-program förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar listan över standardattribut, där som **e-postadress** mappas med **user.mail**. ScaleX Enterprise-programmet förväntar sig att **e-postadressen** mappas med **userprincipalname**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera ScaleX Enterprise** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ScaleX Enterprise.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **ScaleX Enterprise**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-scalex-enterprise-sso"></a>Konfigurera ScaleX Enterprise SSO

1. Om du vill automatisera konfigurationen i ScaleX Enterprise måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren klickar du på **Konfigurera ScaleX Enterprise** kommer att leda dig till ScaleX Enterprise-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på ScaleX Enterprise. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera ScaleX Enterprise manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen ScaleX Enterprise som administratör och utför följande steg:

1. Klicka på menyn längst upp till höger och välj **Contoso Administration**.

    > [!NOTE]
    > Contoso är bara ett exempel. Detta bör vara ditt riktiga företagsnamn.

    ![Konfigurera enkel inloggning](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Välj **Integreringar** på den övre menyn och välj **enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Fyll i formuläret enligt följande:

    ![Konfigurera enkel inloggning](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Välj **Skapa alla användare som kan autentisera med SSO**

    b. **Service Provider saml**: Klistra in värdet ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***

    c. **E-postfältet Identitetsproviders namn i ACS-svar:** Klistra in värdet`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Entity Provider EntityDescriptor Entity ID:** Klistra in **azure AD-identifierare** som kopierats från Azure-portalen.

    e. **SingleSignOnService-URL för identitetsprovider:** Klistra in **inloggnings-URL:en** från Azure-portalen.

    f. **Public X509-certifikat för identitetsprovider:** Öppna X509-certifikatet som hämtats från Azure i anteckningar och klistra in innehållet i den här rutan. Kontrollera att det inte finns några radbrytningar mitt i certifikatinnehållet.

    g. Markera följande kryssrutor: **Aktiverat, Kryptera NameID och Signera AuthnRequests.**

    h. Spara inställningarna genom att klicka på **Uppdatera SSO-inställningar.**

### <a name="create-scalex-enterprise-test-user"></a>Skapa ScaleX Enterprise-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på ScaleX Enterprise måste de etableras i ScaleX Enterprise. När det gäller ScaleX Enterprise är etablering en automatisk uppgift och inga manuella steg krävs. Alla användare som kan autentisera med SSO-autentiseringsuppgifter etableras automatiskt på ScaleX-sidan.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ScaleX Enterprise på åtkomstpanelen bör du automatiskt loggas in på det ScaleX Enterprise som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ScaleX Enterprise med Azure AD](https://aad.portal.azure.com/)