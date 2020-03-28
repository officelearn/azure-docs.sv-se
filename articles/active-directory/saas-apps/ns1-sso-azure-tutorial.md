---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med NS1 SSO för Azure | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NS1 SSO för Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med NS1 SSO för Azure

I den här självstudien får du lära dig hur du integrerar NS1 SSO för Azure med Azure Active Directory (Azure AD). När du integrerar NS1 SSO för Azure med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NS1 SSO för Azure.
* Aktivera dina användare så att de automatiskt loggas in på NS1 SSO för Azure med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* NS1 SSO för Azure single sign-on (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* NS1 SSO för Azure stöder SP och IDP initierade SSO.
* När du har konfigurerat NS1 SSO för Azure kan du framtvinga sessionskontroll. Detta skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Lägga till NS1 SSO för Azure från galleriet

Om du vill konfigurera integreringen av NS1 SSO för Azure i Azure AD måste du lägga till NS1 SSO för Azure från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **NS1 SSO för Azure** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **NS1 SSO för Azure** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurera och testa en azure AD-inloggning för NS1 SSO för Azure

Konfigurera och testa Azure AD SSO med NS1 SSO för Azure med hjälp av en testanvändare som heter **B.Simon**. För SSO att fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i NS1 SSO för Azure.

Här är de allmänna stegen för att konfigurera och testa Azure AD SSO med NS1 SSO för Azure:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.

    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera NS1 SSO för Azure SSO för](#configure-ns1-sso-for-azure-sso)** att konfigurera de enskilda inloggningsinställningarna på programsidan.

    a. **[Skapa en NS1 SSO för Azure-testanvändare](#create-an-ns1-sso-for-azure-test-user)** för att ha en motsvarighet till B.Simon i NS1 SSO för Azure. Den här motsvarigheten är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **NS1 SSO för** Azure-programintegrering. **Manage** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv följande URL i textrutan **Identifierare:**`https://api.nsone.net/saml/metadata`

    b. Skriv en URL som använder följande mönster i textrutan **Svara URL:**`https://api.nsone.net/saml/sso/<ssoid>`

1. Välj **Ange ytterligare webbadresser**och utför följande steg om du vill konfigurera programmet i **återupptan** av SP-initierat läge:

    Skriv följande URL i textrutan **Sign-on-URL:**`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för svars-URL med den faktiska svars-URL:en. Kontakta [NS1 SSO för Azure Client supportteam för](mailto:techops@nsone.net) att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NS1 SSO för Azure-programmet förväntar sig SAML-påståenden i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från avsnittet **Användarattribut & anspråk** på sidan Programintegrering. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för att öppna dialogrutan **Användarattribut.**

    ![Skärmbild av avsnittet Användarattribut & anspråk, med pennikonen markerad](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Välj det attributnamn som ska redigera anspråket.

    ![Skärmbild av avsnittet Användarattribut & anspråk, med attributnamnet markerat](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Välj **Omvandling**.

    ![Skärmbild av avsnittet Hantera anspråk, med omformning markerad](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Gör följande i avsnittet **Hantera omvandling:**

    ![Skärmbild av avsnittet Hantera omformning, med olika fält markerade](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Välj **ExactMailPrefix()** som **omvandling**.

    1. Välj **user.userprincipalname** som **parameter 1**.

    1. Välj **Lägg till**.

    1. Välj **Spara**.

1. Välj kopieringsknappen i avsnittet **SAML-signeringscertifikat** på sidan Konfigurera enkel inloggning med SAML.On the **Set up single sign-on with SAML** page, in the SAML Signing Certificate section, select the copy button. Detta kopierar **appfederationens metadataadress** och sparar den på datorn.

    ![Skärmbild av SAML-signeringscertifikatet, med kopieringsknappen markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** > .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**

   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned värdet som visas i fältet **Lösenord.**
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till NS1 SSO för Azure.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **NS1 SSO för Azure**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i användarlistan i dialogrutan **Användare och grupper.** Välj sedan **knappen Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **knappen Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurera NS1 SSO för Azure SSO

Om du vill konfigurera enkel inloggning på NS1 SSO för Azure-sidan måste du skicka url:en för appfederationsmetadata till [NS1 SSO för Azure-supportteam](mailto:techops@nsone.net). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Skapa en NS1 SSO för Azure-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i NS1 SSO för Azure. Arbeta med NS1 SSO för Azure-supportteam för att lägga till användarna i NS1 SSO för Azure-plattformen. Du kan inte använda enkel inloggning förrän du skapar och aktiverar användare.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer NS1 SSO för Azure-panelen i Åtkomstpanelen bör du automatiskt loggas in på NS1 SSO för Azure som du konfigurerar SSO för. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova NS1 SSO för Azure med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)