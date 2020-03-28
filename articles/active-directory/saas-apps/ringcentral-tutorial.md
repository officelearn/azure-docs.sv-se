---
title: 'Självstudiekurs: Azure Active Directory-integrering med RingCentral | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72991478"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Självstudiekurs: Integrera RingCentral med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar RingCentral med Azure Active Directory (Azure AD). När du integrerar RingCentral med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RingCentral.
* Gör att användarna automatiskt loggas in på RingCentral med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* RingCentral enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* RingCentral stöder **IDP-initierad** SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Lägga till RingCentral från galleriet

Om du vill konfigurera integreringen av RingCentral i Azure AD måste du lägga till RingCentral från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **RingCentral** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **RingCentral** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med RingCentral med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i RingCentral.

Så här konfigurerar och testar du Azure AD SSO med RingCentral:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera RingCentral SSO](#configure-ringcentral-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa RingCentral-testanvändare](#create-ringcentral-test-user)** – om du vill ha en motsvarighet till B.Simon i RingCentral som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **RingCentral** **Azure-portalen**och välj Enkel inloggning . [Azure portal](https://portal.azure.com/) **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    1. Klicka på **Ladda upp metadatafil**.
    1. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.
    1. När metadatafilen har överförts fylls **url-värdena identifierare** och **svar** i automatiskt i avsnittet **Grundläggande SAML-konfiguration.**

    > [!Note]
    > Du får **metadatafilen för Tjänsteleverantören** på ringcentralen SSO-konfigurationssidan som förklaras senare i självstudien.

1. Om du inte har **metadatafil för Service Provider**anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Identifierare:**

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. I textrutan **Svars-URL** anger du en URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till RingCentral.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **RingCentral**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-ringcentral-sso"></a>Konfigurera RingCentral SSO

1. Om du vill automatisera konfigurationen i RingCentral måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Konfigurera RingCentral** kommer att leda dig till RingCentral ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på RingCentral. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera RingCentral manuellt öppnar du ett nytt webbläsarfönster och loggar in på din RingCentral-företagswebbplats som administratör och utför följande steg:

1. Högst upp klickar du på **Verktyg**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigera till **Enkel inloggning**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Klicka på **Redigera** från **steg 1** på sidan Enkel inloggning under **SSO-konfiguration:** **Single Sign-on**

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Gör följande på sidan **Konfigurera enkel inloggning:**

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klicka på **Bläddra** om du vill ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. När metadata har överförts fylls värdena i automatiskt i avsnittet Allmän information för **SSO.**

    c. Under avsnittet **Attributmappning** väljer du **Mappa e-postattribut till** som`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klicka på **Spara**.

    e. Från **steg 2** klickar du på **Hämta** för att hämta **metadatafilen för Tjänsteleverantören** och överför den i avsnittet **Grundläggande SAML-konfiguration** för att automatiskt fylla i URL-värdena **för identifierare** och **svar** i Azure-portalen.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. På samma sida navigerar du till **Aktivera SSO-avsnitt** och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Välj **Aktivera SSO-tjänst**.

    * Välj **Tillåt användare att logga in med SSO- eller RingCentral-autentiseringsuppgifter**.

    * Klicka på **Spara**.

### <a name="create-ringcentral-test-user"></a>Skapa RingCentral-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i RingCentral. Arbeta med [RingCentral Client supportteam](https://success.ringcentral.com/RCContactSupp) för att lägga till användarna i RingCentral-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer RingCentral-panelen på åtkomstpanelen ska du automatiskt loggas in på den RingCentral som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova RingCentral med Azure AD](https://aad.portal.azure.com/)