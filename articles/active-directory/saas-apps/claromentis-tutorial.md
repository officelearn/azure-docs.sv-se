---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Claromentis | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Claromentis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74823223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Claromentis

I den här självstudien får du lära dig hur du integrerar Claromentis med Azure Active Directory (Azure AD). När du integrerar Claromentis med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Claromentis.
* Gör att användarna automatiskt loggas in på Claromentis med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Claromentis enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Claromentis stöder **SP och IDP** initierade SSO
* Claromentis stöder just in time-användaretablering **Just In Time**

## <a name="adding-claromentis-from-the-gallery"></a>Lägga till Claromentis från galleriet

Om du vill konfigurera integreringen av Claromentis i Azure AD måste du lägga till Claromentis från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Claromentis** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Claromentis** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Konfigurera och testa en azure AD-inloggning för Claromentis

Konfigurera och testa Azure AD SSO med Claromentis med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Claromentis.

Om du vill konfigurera och testa Azure AD SSO med Claromentis slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Claromentis SSO](#configure-claromentis-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Claromentis-testanvändare](#create-claromentis-test-user)** – om du vill ha en motsvarighet till B.Simon i Claromentis som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Claromentis**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare** anger du identifierarevärdet enligt organisationens krav.

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren, svars-URL:en och inloggnings-URL:en som förklaras senare i turorial.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Claromentis.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Claromentis.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Claromentis**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-claromentis-sso"></a>Konfigurera Claromentis SSO

1. I ett annat webbläsarfönster loggar du in på Claromentis webbplats som administratör.

1. Klicka på **programikonen** och välj **Admin**.

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config1.png)

1. Välj fliken **Anpassad inloggningshanterare.**

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config2.png)

1. Välj **SAML Config**.

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config3.png)

1. Bläddra ned till **config-avsnittet** på fliken **SAML Config** och gör följande:

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config4.png)

    a. Ange namnet på den tekniska kontaktpersonen i textrutan **För tekniskt kontaktnamn.**

    b. I textrutan **För teknisk kontakt via e-post** anger du e-postadressen till den tekniska kontaktpersonen.

    c. Ange lösenordet i textrutan **Auth Admin Password.**

1. Bläddra ned till **Auth-källor** och utför följande steg:

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config5.png)

    a. I **IDP-textrutan** anger du azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    b. Ange värdet för entitets-ID i textrutan **För entitets-ID.**

    c. Ladda upp **XML-filen federationsmetadata** som du har hämtat från Azure-portalen.

    d. Klicka på **Spara**.

1. Du kommer nu att märka att alla webbadresser har fyllts i i avsnittet **Identitetsprovider** i avsnittet **SAML Config.**

    ![Claromentis-konfiguration](./media/claromentis-tutorial/config6.png)

    a. Kopiera **identifierare (entitets-ID)** värde, klistra in det här värdet i **textrutan Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Kopiera **url-värdet för svar,** klistra in det här värdet i textrutan **Svara url** på avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    c. Kopiera **url-värdet för inloggning,** klistra in det här värdet i textrutan Sign-on URL på avsnittet **Grundläggande SAML-konfiguration** i **Azure-portalen.**

### <a name="create-claromentis-test-user"></a>Skapa Claromentis-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Claromentis. Claromentis stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Claromentis skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Claromentis på åtkomstpanelen ska du automatiskt loggas in på den Claromentis som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Claromentis med Azure AD](https://aad.portal.azure.com/)