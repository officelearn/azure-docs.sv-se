---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Slack | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c80963976783321d05fc6f32bb24daed36fa105
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Slack

I den här självstudien får du lära dig hur du integrerar Slack med Azure Active Directory (Azure AD). När du integrerar Slack med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Slack.
* Gör att användarna automatiskt loggas in på Slack med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Slack enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Slack har stöd för **SP**-initierad enkel inloggning
* Slack stöder **just-in-time**-användaretablering
* Slack har stöd för [**automatisk** användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* När du har konfigurerat Slack kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-slack-from-the-gallery"></a>Lägga till Slack från galleriet

För att kunna konfigurera integreringen av Slack i Azure AD måste du lägga till Slack från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Slack** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Slack** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Konfigurera och testa en azure AD-inloggning för Slack

Konfigurera och testa Azure AD SSO med Slack med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Slack.

Om du vill konfigurera och testa Azure AD SSO med Slack slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Slack SSO](#configure-slack-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Slack-testanvändare](#create-slack-test-user)** – om du vill ha en motsvarighet till B.Simon i Slack som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **Slack**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.slack.com`

    b. Skriv en URL i textrutan **Identifierare (enhets-ID):**`https://slack.com`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Slack-kundsupporten](https://slack.com/help/contact) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Slack-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Slack-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav. Om användarna inte har e-postadress sedan mappa **e-postadressen** till **user.userprincipalname**.

    | Namn | Källattribut |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | | |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera slack.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Slack.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Slack** i programlistan.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-slack-sso"></a>Konfigurera Slack SSO

1. Logga in på slackföretagets webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **Microsoft Azure AD** och sedan till **Teaminställningar**.

     ![Konfigurera enkel inloggning på appsidan](./media/slack-tutorial/tutorial_slack_001.png)

3. I avsnittet **Teaminställningar** klickar du på fliken **Autentisering** och sedan på **Ändra inställningar**.

    ![Konfigurera enkel inloggning på appsidan](./media/slack-tutorial/tutorial_slack_002.png)

4. Gör följande i dialogrutan **Inställningar för SAML-autentisering**:

    ![Konfigurera enkel inloggning på appsidan](./media/slack-tutorial/tutorial_slack_003.png)

    a.  I textrutan **SAML 2.0 Endpoint (HTTP)** (SAML 2.0-slutpunkt (HTTP)) klistrar du in värdet för **inloggnings-URL:en**, som du har kopierat från Azure-portalen.

    b.  I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen.

    c.  Öppna den nedladdade certifikatfilen i Anteckningar, kopiera innehållet i den i Urklipp och klistra in den i textrutan **Offentligt certifikat**.

    d. Konfigurera de tre inställningarna ovan efter behov för ditt Slack-team. Mer information om inställningarna finns i **Slacks konfigurationsguide för enkel inloggning** här. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klicka på **Spara konfiguration**.

### <a name="create-slack-test-user"></a>Skapa Slack-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i Slack. Slack har stöd för just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att få åtkomst till Slack, om den inte redan finns. Slack stöder även automatisk användaretablering. Mer information finns [här](slack-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Slack-supporten](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect är synkroniseringsverktyget som kan synkronisera lokala Active Directory-identiteter till Azure AD och sedan kan dessa synkroniserade användare också använda programmen som andra molnanvändare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Slack-panelen i åtkomstpanelen bör du automatiskt loggas in på Slack som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Slack med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)