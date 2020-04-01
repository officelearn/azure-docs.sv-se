---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Azure AD SAML Toolkit | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Azure AD SAML Toolkit

I den här självstudien får du lära dig hur du integrerar Azure AD SAML Toolkit med Azure Active Directory (Azure AD). När du integrerar Azure AD SAML Toolkit med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Azure AD SAML Toolkit.
* Aktivera dina användare så att de automatiskt loggas in på Azure AD SAML Toolkit med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Azure AD SAML Toolkit enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Azure AD SAML Toolkit stöder **SP** initierad SSO
* När du har konfigurerat Azure AD SAML Toolkit kan du framtvinga Sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Lägga till Azure AD SAML Toolkit från galleriet

Om du vill konfigurera integreringen av Azure AD SAML Toolkit i Azure AD måste du lägga till Azure AD SAML Toolkit från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Azure AD SAML Toolkit** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Azure AD SAML Toolkit** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Konfigurera och testa en azure AD-inloggning för Azure AD SAML Toolkit

Konfigurera och testa Azure AD SSO med Azure AD SAML Toolkit med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Azure AD SAML Toolkit.

Så här konfigurerar och testar du Azure AD SSO med Azure AD SAML Toolkit:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Azure AD SAML Toolkit-testanvändare](#create-azure-ad-saml-toolkit-test-user)** – om du vill ha en motsvarighet till B.Simon i Azure AD SAML Toolkit som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Azure AD SAML Toolkit-programintegration** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I rutan **Inloggnings-URL** anger du en URL: `https://samltoolkit.azurewebsites.net/`

    b. Skriv en URL i textrutan **Identifierare (enhets-ID):**`https://samltoolkit.azurewebsites.net`

    c. Skriv en URL i textrutan **Svars-URL**: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Azure AD SAML Toolkit.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Azure AD SAML Toolkit.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Azure AD **SAML Toolkit**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurera Azure AD SAML Toolkit SSO

1. Öppna ett nytt webbläsarfönster, om du inte har registrerat dig på Webbplatsen Azure AD SAML Toolkit, registrerar dig först genom att klicka på **registrera dig**. Om du redan har registrerat dig loggar du in på webbplatsen för Ditt Azure AD SAML Toolkit-företag med de registrerade inloggningsuppgifterna.

    ![Azure AD SAML-verktygslådaregister](./media/saml-toolkit-tutorial/register.png)

1. Klicka på **SAML-konfigurationen**.

    ![Saml-konfiguration för Azure AD SAML-verktygslåda](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klicka på **Skapa**.

    ![Azure AD SAML Toolkit Skapa SSO](./media/saml-toolkit-tutorial/createsso.png)

1. Gör följande på sidan **SAML SSO-konfiguration:**

    ![Azure AD SAML Toolkit Skapa SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. I textrutan **Inloggnings-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    1. I textrutan **Azure AD Identifier** klistrar du in Azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    1. I textrutan **Utloggnings-URL** klistrar du värdet för **Utloggnings-URL**, som du har kopierat från Azure-portalen.

    1. Klicka på **Välj fil** och ladda upp filen **Certificate (Raw)** som du har hämtat från Azure-portalen.

    1. Klicka på **Skapa**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Skapa Azure AD SAML Toolkit-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Azure AD SAML Toolkit. Azure AD SAML Toolkit stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Azure AD SAML Toolkit skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Azure AD SAML Toolkit på åtkomstpanelen bör du automatiskt loggas in i Azure AD SAML Toolkit som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Azure AD SAML Toolkit med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Azure AD SAML Toolkit med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-azure)