---
title: 'Självstudiekurs: Azure Active Directory-integrering med ICIMS | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ICIMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 72dbd649-e4b1-4d72-ad76-636d84922596
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9706f28e6ca442543f566a574f437a0ec68c4e0
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583739"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-icims"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ICIMS

I den här självstudien får du lära dig hur du integrerar ICIMS med Azure Active Directory (Azure AD). När du integrerar ICIMS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ICIMS.
* Aktivera dina användare så att de automatiskt loggas in på ICIMS med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* ICIMS enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* ICIMS har stöd för **SP**-initierad enkel inloggning
* När du har konfigurerat ICIMS kan du framtvinga sessionskontroll, vilket skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-icims-from-the-gallery"></a>Lägga till ICIMS från galleriet

För att konfigurera integreringen av ICIMS i Azure AD behöver du lägga till ICIMS från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ICIMS** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ICIMS** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-icims"></a>Konfigurera och testa en azure AD-inloggning för ICIMS

Konfigurera och testa Azure AD SSO med ICIMS med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ICIMS.

Så här konfigurerar och testar du Azure AD SSO med ICIMS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera ICIMS SSO](#configure-icims-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa ICIMS-testanvändare](#create-icims-test-user)** – om du vill ha en motsvarighet till B.Simon i ICIMS som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **ICIMS** **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant name>.icims.com`

    b. Skriv en URL i textrutan **Identifierare:**`icims.com`

    c. Skriv en URL i textrutan **Svars-URL**: `https://.icims.com/`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [ICIMS Client support team](https://www.icims.com/contact-us) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera ICIMS** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ICIMS.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **ICIMS**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-icims-sso"></a>Konfigurera ICIMS SSO

För att konfigurera enkel inloggning på **ICIMS**-sidan behöver du skicka den nedladdade **XML:en med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för ICIMS](https://www.icims.com/contact-us). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-icims-test-user"></a>Skapa ICIMS-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i ICIMS. Arbeta med [ICIMS supportteam](https://www.icims.com/contact-us) för att lägga till användarna i ICIMS-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ICIMS-panelen i åtkomstpanelen bör du automatiskt loggas in på ICIMS som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ICIMS med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)