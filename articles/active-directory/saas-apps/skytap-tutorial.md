---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Enkel inloggning för Skytap | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Enkel inloggning för Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565799"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med enkel inloggning för Skytap

I den här självstudien får du lära dig hur du integrerar Enkel inloggning för Skytap med Azure Active Directory (Azure AD). När du integrerar Enkel inloggning för Skytap med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Enkel inloggning för Skytap.
* Gör att användarna automatiskt loggas in på Enkel inloggning för Skytap med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Enkel inloggning för Skytap enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Enkel inloggning för Skytap stöder SP och IDP initierade SSO.
* När du har konfigurerat Enkel inloggning för Skytap kan du framtvinga sessionskontrollen. Detta skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Lägg till enkel inloggning för Skytap från galleriet

Om du vill konfigurera integreringen av enkel inloggning för Skytap i Azure AD måste du lägga till enkel inloggning för Skytap från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Enkel inloggning för Skytap** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Enkel inloggning för Skytap** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurera och testa en azure AD-inloggning för enkel inloggning för Skytap

Konfigurera och testa Azure AD SSO med Enkel inloggning för Skytap med hjälp av en testanvändare som heter **B.Simon**. För SSO att fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i Enkel inloggning för Skytap.

Här är de allmänna stegen för att konfigurera och testa Azure AD SSO med Enkel inloggning för Skytap:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.

    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera enkel inloggning för Skytap SSO](#configure-single-sign-on-for-skytap-sso)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.

    a. **[Skapa en enkel inloggning för Skytap-testanvändare](#create-single-sign-on-for-skytap-test-user)** för att ha en motsvarighet till B.Simon i Enkel inloggning för Skytap. Den här motsvarigheten är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **Enkel inloggning för Skytap-programintegration.** **Manage** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL som använder följande mönster i textrutan **Identifierare:**`http://pingone.com/<custom EntityID>`

    b. Skriv en URL som använder följande mönster i textrutan **Svara URL:**`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Välj **Ange ytterligare webbadresser**och utför följande steg om du vill konfigurera programmet i **återupptastartat SP-läge:**

    a. Skriv en URL som använder följande mönster i textrutan **Sign-on-URL:**`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Skriv en URL som använder följande mönster i textrutan **Återlämningstillstånd:**`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren, svars-URL:en, inloggnings-URL och Relay State. Kontakta [supportteamet för enkel inloggning för Skytap Client](mailto:support@skytap.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Leta reda på XML för **federationsmetadata**i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML.** Välj **Hämta** om du vill hämta metadatafilen och spara den på datorn.

    ![Skärmbild av länken för hämtning av certifikat](common/metadataxml.png)

1. Kopiera lämplig WEBBADRESS eller webbadresser på avsnittet **Konfigurera enkel inloggning för Skytap,** baserat på dina krav.

    ![Skärmbild av url:er för kopieringskonfiguration](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Enkel inloggning för Skytap.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj Enkel inloggning **för Skytap**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i användarlistan i dialogrutan **Användare och grupper.** Välj sedan **knappen Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **knappen Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurera enkel inloggning för Skytap SSO

Om du vill konfigurera enkel inloggning på den enskilda inloggningen för Skytap-sidan måste du skicka den nedladdade **XML-koden för federationsmetadata**och lämpliga kopierade url:er från Azure-portalen till [supportteamet för enkel inloggning för Skytap-klienten](mailto:support@skytap.com). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Skapa enkel inloggning för Skytap-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Enkel inloggning för Skytap. Arbeta med [supportteamet för enkel inloggning för Skytap-klienten för](mailto:support@skytap.com) att lägga till användarna i plattformen Single Sign-on for Skytap. Du kan inte använda enkel inloggning förrän du skapar och aktiverar användare.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer panelen Enkel inloggning för Skytap på Åtkomstpanelen ska du automatiskt loggas in på den enda inloggning för Skytap som du konfigurerar SSO för. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Slack med Azure AD](https://aad.portal.azure.com/)

