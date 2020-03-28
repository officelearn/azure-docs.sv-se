---
title: 'Självstudiekurs: Azure Active Directory-integrering med Säkerhetskopiering av Carbonite-slutpunkter | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Carbonite Endpoint Backup.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879681"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Självstudiekurs: Integrera säkerhetskopiering av Carbonite-slutpunkter med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Carbonite Endpoint Backup med Azure Active Directory (Azure AD). När du integrerar Carbonite Endpoint Backup med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Carbonite Endpoint Backup.
* Gör att användarna automatiskt loggas in på Carbonite Endpoint Backup med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Carbonite Endpoint Backup enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Carbonite Endpoint Backup stöder **SP och IDP** initierad SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Lägga till Carbonite Slutpunkt Backup från galleriet

Om du vill konfigurera integreringen av Carbonite Endpoint Backup i Azure AD måste du lägga till Carbonite Endpoint Backup från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Säkerhetskopia av **Carbonite Slutpunkt** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Carbonite Slutpunktssäkerhetskopiering** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Carbonite Slutpunktssäkerhetskopiering med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Carbonite Endpoint Backup.

Så här konfigurerar och testar du Azure AD SSO med Carbonite Endpoint Backup:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Carbonite Endpoint Backup SSO](#configure-carbonite-endpoint-backup-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Carbonite Slutpunkt Backup testanvändare](#create-carbonite-endpoint-backup-test-user)** - att ha en motsvarighet till B.Simon i Carbonite Slutpunkt Backup som är kopplad till Azure AD representation av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Carbonite Endpoint** **Backup-program**och väljer Enkel inloggning .
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en av följande webbadresser i textrutan **Identifierare:**

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Skriv en av följande webbadresser i textrutan **Svara** url:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en av följande webbadresser i textrutan **Sign-on-URL:**

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera carbonite-slutpunktssäkerhet.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurera SSO för säkerhetskopiering av carbonite-slutpunkt

1. Om du vill automatisera konfigurationen i Carbonite Endpoint Backup måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Carbonite Endpoint Backup** leder dig till Carbonite Endpoint Backup-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Carbonite Endpoint Backup. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Carbonite Endpoint Backup manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats för Carbonite-säkerhetskopiering som administratör och utför följande steg:

4. Klicka på **företaget** från den vänstra rutan.

    ![Konfiguration av säkerhetskopiering av karbonit-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klicka på **Enkel inloggning**.

    ![Konfiguration av säkerhetskopiering av karbonit-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klicka på **Aktivera** och sedan på **Redigera inställningar** som ska konfigureras.

    ![Konfiguration av säkerhetskopiering av karbonit-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Gör följande på sidan **Inställningar för enkel inloggning:**

    ![Konfiguration av säkerhetskopiering av karbonit-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Klistra in azure **AD-identifierare** i textrutan **för identitetsproviderns namn,** som du har kopierat från Azure-portalen.

    1. I **textrutan URL för identitetsprovider** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    1. Klicka på **Välj fil** för att ladda upp den nedladdade **Certificate(Base64)** filen från Azure-portalen.

    1. Klicka på **Spara**.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Carbonite Endpoint Backup.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Carbonite Endpoint Backup**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-carbonite-endpoint-backup-test-user"></a>Skapa testanvändare för Carbonite-slutpunktssäkerhet

1. I ett annat webbläsarfönster loggar du in på företagets webbplats för Carbonite-slutpunktssäkerhet som administratör.

1. Klicka på **användarna** från den vänstra rutan och klicka sedan på **Lägg till användare**.

    ![Lägg till användare i säkerhetskopiering av carbonite-slutpunkter](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Gör följande på sidan **Lägg till användare:**

    ![Lägg till användare i säkerhetskopiering av carbonite-slutpunkter](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Ange **e-post**, **Förnamn**, **Användarens efternamn** och ange de behörigheter som krävs för användaren enligt organisationens krav.

    1. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Säkerhetskopiering av Carbonite-slutpunkter på åtkomstpanelen bör du automatiskt loggas in på den säkerhetskopia av Carbonite-slutpunkten som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)