---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med KnowledgeOwl | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72791641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med KnowledgeOwl

I den här självstudien får du lära dig hur du integrerar KnowledgeOwl med Azure Active Directory (Azure AD). När du integrerar KnowledgeOwl med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till KnowledgeOwl.
* Gör att användarna automatiskt loggas in på KnowledgeOwl med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* KnowledgeOwl enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* KnowledgeOwl stöder **SP och IDP** initierade SSO
* KnowledgeOwl stöder just **in time-användares** etablering

## <a name="adding-knowledgeowl-from-the-gallery"></a>Lägga till KnowledgeOwl från galleriet

Om du vill konfigurera integreringen av KnowledgeOwl i Azure AD måste du lägga till KnowledgeOwl från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **KnowledgeOwl** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **KnowledgeOwl** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Konfigurera och testa en azure AD-inloggning för KnowledgeOwl

Konfigurera och testa Azure AD SSO med KnowledgeOwl med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i KnowledgeOwl.

Så här konfigurerar och testar du Azure AD SSO med KnowledgeOwl:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera KnowledgeOwl SSO](#configure-knowledgeowl-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa KnowledgeOwl-testanvändare](#create-knowledgeowl-test-user)** – om du vill ha en motsvarighet till B.Simon i KnowledgeOwl som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **KnowledgeOwl-programintegration** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera det här värdet från den faktiska identifieraren, svars-URL:en och inloggnings-URL: n som förklaras senare i självstudien.

1. KnowledgeOwl-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig KnowledgeOwl ansökan få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut | Namnområde |
    | ------------ | -------------------- | -----|
    | ssoid (ssoid) | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera KnowledgeOwl** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till KnowledgeOwl.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **KnowledgeOwl**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-knowledgeowl-sso"></a>Konfigurera KnowledgeOwl SSO

1. Logga in på webbplatsen KnowledgeOwl som administratör i ett annat webbläsarfönster.

1. Klicka på **Inställningar** och välj sedan **Säkerhet**.

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure1.png)

1. Bläddra till **SAML SSO-integrering** och utför följande steg:

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure2.png)

    a. Välj **Aktivera SAML SSO**.

    b. Kopiera **SP-entitets-ID-värdet** och klistra in det i **identifieraren (entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Kopiera **SP-inloggnings-URL-värdet** och klistra in det i textrutorna **Sign-on URL och Svara på URL** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    d. I **IdP-entitetstextrutan** klistrar du in Azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    e. I textrutan **IdP-inloggningsadress** klistrar du in värdet **för inloggningsadressen,** som du har kopierat från Azure-portalen.

    f. I **textrutan URL för IdP-utloggning** klistrar du in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen

    g. Ladda upp det hämtade certifikatformuläret för Azure-portalen genom att klicka på **Upload IdP-certifikatet**.

    h. Klicka på **Mappa SAML-attribut** för att mappa attribut och utför följande steg:

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure3.png)

    * Gå `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` in i **SSO-ID-textrutan**
    * Skriv `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in i **textrutan Användarnamn/E-post.**
    * Skriv `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in textrutan **Förnamn.**
    * Skriv `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in i textrutan **Efternamn.**
    * Klicka på **Spara**

    i. Klicka på **Spara** längst ned på sidan.

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Skapa KnowledgeOwl-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i KnowledgeOwl. KnowledgeOwl stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i KnowledgeOwl skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [KnowledgeOwl supportteam](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen KnowledgeOwl på åtkomstpanelen ska du automatiskt loggas in på den KnowledgeOwl som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova KnowledgeOwl med Azure AD](https://aad.portal.azure.com/)