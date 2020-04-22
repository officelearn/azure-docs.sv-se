---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med InVision | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InVision.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02487206-30b0-4b1d-ae99-573c3d2ef9b0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2427fff37158745e416f4b2f0641697ad62ea9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682666"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med InVision

I den här självstudien får du lära dig hur du integrerar InVision med Azure Active Directory (Azure AD). När du integrerar InVision med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till InVision.
* Gör att användarna automatiskt loggas in på InVision med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* InVision enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* InVision stöder **SP och IDP** initierade SSO
* När du har konfigurerat InVision kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>Lägga till InVision från galleriet

Om du vill konfigurera integreringen av InVision i Azure AD måste du lägga till InVision från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **InVision** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **InVision** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Konfigurera och testa en azure AD-inloggning för InVision

Konfigurera och testa Azure AD SSO med InVision med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i InVision.

Så här konfigurerar och testar du Azure AD SSO med InVision:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera InVision SSO](#configure-invision-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa InVision-testanvändare](#create-invision-test-user)** - om du vill ha en motsvarighet till B.Simon i InVision som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **InVision-programintegrering** på **InVision** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<SUBDOMAIN>.invisionapp.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [InVision Client support team](mailto:support@invisionapp.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera InVision** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till InVision.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **InVision**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-invision-sso"></a>Konfigurera InVision SSO

1. Logga in på InVision-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **Team** och välj **Inställningar**.

    ![InVision-konfiguration](./media/invision-tutorial/config1.png)

1. Bläddra ned till **Enkel inloggning** och klicka sedan på **Ändra**.

    ![InVision-konfiguration](./media/invision-tutorial/config3.png)

1. Gör följande på sidan **Enkel inloggning:**

    ![InVision-konfiguration](./media/invision-tutorial/config4.png)

    a. Ändra **Kräv SSO för varje medlem i < kontonamn >** till **På**.

    b. Ange namnet i **namntextrutan,** till `azureadsso`exempel .

    c. Ange värdet för inloggnings-URL i textrutan **Inloggnings-URL.**

    d. I textrutan Logga ut URL klistrar du in **url-värdet** **För logg,** som du har kopierat från Azure-portalen.

    e. Öppna det nedladdade **certifikatet (Base64)** i Anteckningar i **SMS:en** i SMS:en, kopiera innehållet och klistra in det i SMS-certifikatet.

    f. Använd `Unspecified` för **namn-ID-format i**textrutan **Namn-ID.**

    g. Välj **SHA-256** i listrutan för **HASH-algoritmen**.

    h. Ange lämpligt namn på **SSO-knappetiketten**.

    i. Gör **Tillåt just-in-time-etablering** på.

    j. Klicka på **Uppdatera**.

### <a name="create-invision-test-user"></a>Skapa InVision-testanvändare

1. Logga in på InVision-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **Team** och välj **Personer**.

    ![InVision-konfiguration](./media/invision-tutorial/config2.png)

1. Klicka på **+ ICON** för att lägga till nya användare.

    ![InVision-konfiguration](./media/invision-tutorial/user1.png)

1. Ange användarens e-postadress och klicka på **Nästa**.

    ![InVision-konfiguration](./media/invision-tutorial/user2.png)

1. När du har verifierat e-postadressen och klicka sedan på **Bjud in**.

    ![InVision-konfiguration](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på indelningspanelen på åtkomstpanelen ska du automatiskt loggas in på den InVision som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova InVision med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du InVision med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)