---
title: 'Självstudiekurs: Integrering av Enkel inloggning i Azure Active Directory med sändebud | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Envoy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28f3fca731c9ceb28f66ecd1c178e5c025f80ede
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70163533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Envoy

I den här självstudien får du lära dig hur du integrerar Sändebud med Azure Active Directory (Azure AD). När du integrerar Sändebud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Envoy.
* Gör att användarna automatiskt loggas in på Envoy med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Envoy single sign-on (SSO) aktiverade prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Envoy har stöd för **SP**-initierad enkel inloggning

* Envoy har stöd för **just-in-time**-användaretablering

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-envoy-from-the-gallery"></a>Lägga till Envoy från galleriet

För att konfigurera integrering av Envoy i Azure AD behöver du lägga till Envoy från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Sändebud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Sändebud** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-envoy"></a>Konfigurera och testa en enda Azure AD-inloggning för Envoy

Konfigurera och testa Azure AD SSO med Envoy med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Envoy.

Så här konfigurerar och testar du Azure AD SSO med Envoy:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Envoy SSO](#configure-envoy-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa envoy-testanvändare](#create-envoy-test-user)** – om du vill ha en motsvarighet till B.Simon i Sändebud som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På [Azure portal](https://portal.azure.com/)sidan Integrering av **envoy-program**på sidan **Programintegrering för Sändebud** hittar du avsnittet **Hantera** och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för Envoy](https://envoy.com/contact/) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera sändebud** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Envoy.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Envoy**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-envoy-sso"></a>Konfigurera SSO för sändebud

1. Om du vill automatisera konfigurationen i Envoy måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup Sändebud** kommer att hänvisa dig till Sändebud ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Envoy. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Envoy manuellt öppnar du ett nytt webbläsarfönster och loggar in på din webbplats för sändebud som administratör och utför följande steg:

4. I verktygsfältet längst upp klickar du på **Inställningar**.

    ![Envoy](./media/envoy-tutorial/ic776782.png "Envoy")

5. Klicka på **Företag**.

    ![Företag](./media/envoy-tutorial/ic776783.png "Företag")

6. Klicka på **SAML**.

    ![SAML](./media/envoy-tutorial/ic776784.png "SAML")

7. I avsnittet för konfiguration av **SAML-autentisering** utför du följande steg:

    ![SAML-autentisering](./media/envoy-tutorial/ic776785.png "SAML-autentisering")
    
    >[!NOTE]
    >Värdet för HQ-plats-ID genereras automatiskt av programmet.
    
    a. I textrutan **Fingeravtryck** klistrar du in värdet för det **Tumavtryck** för certifikatet som du har kopierat från Azure-portalen.
    
    b. Klistra in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IDENTITY PROVIDER HTTP SAML URL** (identitetsproviderns HTTP SAML-URL).
    
    c. Klicka på **Spara ändringar**.

### <a name="create-envoy-test-user"></a>Skapa Envoy-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Envoy. Envoy stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Sändebud skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Envoy-panelen i åtkomstpanelen bör du automatiskt loggas in på Envoy som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Sändebud med Azure AD](https://aad.portal.azure.com/)

