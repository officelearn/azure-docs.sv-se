---
title: 'Självstudiekurs: Azure Active Directory-integrering med Civic Platform | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Civic Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496828"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Självstudiekurs: Integrera Civic Platform med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Civic Platform med Azure Active Directory (Azure AD). När du integrerar Civic Platform med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Civic Platform.
* Gör det möjligt för användarna att automatiskt loggas in på Civic Platform med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* En enda inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Civic Platform stöder **SP** initierade SSO





## <a name="adding-civic-platform-from-the-gallery"></a>Lägga civic plattform från galleriet

Om du vill konfigurera integreringen av Civic Platform i Azure AD måste du lägga till Civic Platform från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Civic Platform** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Civic Platform** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Civic Platform med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Civic Platform.

Så här konfigurerar och testar du Azure AD SSO med Civic Platform:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SSO för civic platform](#configure-civic-platform-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. Skapa användare av **[Civic Platform-test](#create-civic-platform-test-user)** - om du vill ha en motsvarighet till B.Simon i Civic Platform som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Civic Platform** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.accela.com`

    b. Skriv en URL i textrutan **Identifierare (enhets-ID):**`civicplatform.accela.com`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Civic Platform Client supportteam](mailto:skale@accela.com) för att få detta värde. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. Navigera till **Azure Active Directory** > **App-registreringar** i Azure AD, välj ditt program.

1. Kopiera **katalog-ID:t (klienten)** och lagra det i Anteckningar.

    ![Kopiera katalogen (klient-ID) och lagra den i appkoden](media/civic-platform-tutorial/directoryid.png)

1. Kopiera **program-ID:t** och lagra det i Anteckningar.

   ![Kopiera programmets (klient)-ID](media/civic-platform-tutorial/applicationid.png)

1. Navigera till **Azure Active Directory** > **App-registreringar** i Azure AD, välj ditt program. Välj **Certifikat & hemligheter**.

1. Välj **Klienthemligheter -> Ny klienthemlighet**.

1. Ange en beskrivning av hemligheten och en varaktighet. När du är klar väljer du **Lägg till**.

   > [!NOTE]
   > När du har sparat klienthemligheten visas värdet för klienthemligheten. Kopiera det här värdet eftersom du inte kan hämta nyckeln senare.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurera Medborgerlig plattform SSO

1. Öppna ett nytt webbläsarfönster och logga in på webbplatsen atlassianska molnföretag som administratör.

1. Klicka på **Standardval**.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/standard-choices.png)

1. Skapa ett standardval **ssoconfig**.

1. Sök efter **ssoconfig** och skicka.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config.png)

1. Expandera SSOCONFIG genom att klicka på röd punkt.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config01.png)

1. Ange SSO-relaterad konfigurationsinformation i följande steg:

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config02.png)

    1. I **fältet applicationid** anger du **värdet Program-ID,** som du har kopierat från Azure-portalen.

    1. I fältet **clientSecret** anger du det **hemliga** värdet, som du har kopierat från Azure-portalen.

    1. I **fältet directoryId** anger du värdet **Katalog (klient) som** du har kopierat från Azure-portalen.

    1. Ange idpName. Ex:- `Azure`.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Civic Platform.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Civic Platform**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-civic-platform-test-user"></a>Skapa användare av testanvändare för Civic Platform

I det här avsnittet skapar du en användare som heter B.Simon i Civic Platform. Arbeta med Supportteamet för Civic Platform för att lägga till användarna i supportteamet för [Civic Platform Client.](mailto:skale@accela.com) Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Civic Platform i åtkomstpanelen ska du automatiskt loggas in på den medborgarplattform som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

