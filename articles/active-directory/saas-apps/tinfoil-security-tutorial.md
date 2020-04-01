---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med TINFOIL SECURITY | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74170768"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med TINFOIL SECURITY

I den här självstudien får du lära dig hur du integrerar TINFOIL SECURITY med Azure Active Directory (Azure AD). När du integrerar TINFOIL SECURITY med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TINFOIL SECURITY.
* Gör att användarna automatiskt loggas in på TINFOIL SECURITY med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* OERSKÄRPA SECURITY enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* TINFOIL SECURITY stöder **IDP** initierad SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Lägga till TINFOIL SECURITY från galleriet

Om du vill konfigurera integreringen av TINFOIL SECURITY i Azure AD måste du lägga till TINFOIL SECURITY från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **TINFOIL SECURITY** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **TINFOIL SECURITY** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Konfigurera och testa en azure AD-inloggning för TINFOIL SECURITY

Konfigurera och testa Azure AD SSO med TINFOIL SECURITY med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i TINFOIL SECURITY.

Så här konfigurerar och testar du Azure AD SSO med TINFOIL SECURITY:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera TINFOIL SECURITY SSO](#configure-tinfoil-security-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa TINFOIL SECURITY-testanvändare](#create-tinfoil-security-test-user)** – om du vill ha en motsvarighet till B.Simon i TINFOIL SECURITY som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj enkel inloggning på sidan FÖR INTEGRERING AV **TINFOIL** **SECURITY-program.**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Visitly-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Visitly ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut |
    | ------------------- | -------------|
    | accountId | UXXXXXXXXXXX |

    > [!NOTE]
    > Du kommer att få accountid värde förklaras senare i handledningen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera TINFOIL SECURITY.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TINFOIL SECURITY.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **TINFOIL SECURITY**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-tinfoil-security-sso"></a>Konfigurera TINFOIL SECURITY SSO

1. I ett annat webbläsarfönster loggar du in på webbplatsen för TINFOIL SECURITY som administratör.

1. Klicka på **Mitt konto**i verktygsfältet högst upp .

    ![Instrumentpanelen](./media/tinfoil-security-tutorial/ic798971.png "Instrumentpanel")

1. Klicka på **Säkerhet**.

    ![Säkerhet](./media/tinfoil-security-tutorial/ic798972.png "Säkerhet")

1. På sidan för konfiguration av **Enkel inloggning** utför du följande steg:

    ![Enkel inloggning](./media/tinfoil-security-tutorial/ic798973.png "för Aha!")

    a. Välj **Aktivera SAML**.

    b. Klicka på **Manuell konfiguration**.

    c. I **SMS-textrutan för SIM-postadress** klistrar du in värdet **för inloggningsadressen** som du har kopierat från Azure-portalen

    d. Klistra in värdet **för tumavtryck** som du har kopierat från avsnittet **SAML-signeringscertifikat** i SMS:et för **SAML-certifikat.**
  
    e. Kopiera **värdet för ditt konto-ID** och klistra in värdet i textrutan **Källattribut** under **användarattribut & anspråk** i Azure-portalen.

    f. Klicka på **Spara**.

### <a name="create-tinfoil-security-test-user"></a>Skapa testanvändare för TINFOIL SECURITY

För att Azure AD-användare ska kunna logga in på TINFOIL SECURITY måste de etableras i TINFOIL SECURITY. När det gäller TINFOIL SECURITY är etablering en manuell aktivitet.

**Så här hämtar du en användare som etableras:**

1. Om användaren är en del av ett Enterprise-konto måste du [kontakta STÖDET FÖR TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) för att få användarkontot skapat.

1. Om användaren är en vanlig TINFOIL SECURITY SaaS-användare kan användaren lägga till en medarbetare på någon av användarens webbplatser. Detta utlöser en process för att skicka en inbjudan till det angivna e-postmeddelandet för att skapa ett nytt TINFOIL SECURITY-användarkonto.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa ett annat användarkonto för TINFOIL SECURITY för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ALUMINIUMFOLIE SECURITY på åtkomstpanelen bör du automatiskt loggas in på den TINFOIL-SÄKERHET som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova TINFOIL SECURITY med Azure AD](https://aad.portal.azure.com/)