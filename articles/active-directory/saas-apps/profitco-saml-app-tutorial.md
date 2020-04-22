---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Profit.co SAML-app | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Profit.co SAML-app.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Profit.co SAML-app

I den här självstudien får du lära dig hur du integrerar Profit.co SAML-app med Azure Active Directory (Azure AD). När du integrerar Profit.co SAML App med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Profit.co SAML-app.
* Gör att användarna automatiskt loggas in på Profit.co SAML-app med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Profit.co SAML App enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Profit.co SAML App stöder IDP initierad SSO.

* När du har konfigurerat Profit.co SAML-app kan du framtvinga sessionskontrollen. Detta skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Lägg till Profit.co SAML-appen från galleriet

Om du vill konfigurera integreringen av Profit.co SAML-app i Azure AD måste du lägga till Profit.co SAML-appen från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Profit.co SAML-appen** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Profit.co SAML App** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Konfigurera och testa en azure AD-inloggning för Profit.co SAML-app

Konfigurera och testa Azure AD SSO med Profit.co SAML App med hjälp av en testanvändare som heter **B.Simon**. För SSO att fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i Profit.co SAML-app.

Här är de allmänna stegen för att konfigurera och testa Azure AD SSO med Profit.co SAML App:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera Profit.co SAML App SSO](#configure-profitco-saml-app-sso)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Profit.co SAML App testanvändare](#create-a-profitco-saml-app-test-user)** att ha en motsvarighet till B.Simon i Profit.co SAML App. Den här motsvarigheten är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan **Profit.co** SAML App-programintegration på **Azure-portalen.** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda i Azure. Användarna måste spara konfigurationen genom att välja knappen **Spara.**

1. Välj knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan Konfigurera enkel inloggning med SAML.On the **Set up single sign-on with SAML,** in the SAML Signing Certificate section, select the **Copy** button. Detta kopierar **appfederationens metadataadress** och sparar den på datorn.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Profit.co SAML App.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **Profit.co SAML App**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i användarlistan i dialogrutan **Användare och grupper.** Välj sedan **knappen Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **knappen Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-profitco-saml-app-sso"></a>Konfigurera Profit.co SAML App SSO

Om du vill konfigurera enkel inloggning på Profit.co SAML-appsidan måste du skicka url:en för App Federation Metadata till [supportteamet för Profit.co SAML App](mailto:support@profit.co). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.

### <a name="create-a-profitco-saml-app-test-user"></a>Skapa en Profit.co TESTANVÄNDARE FÖR SAML-app

I det här avsnittet skapar du en användare som heter B.Simon i Profit.co SAML App. Arbeta med [supportteamet för Profit.co SAML App](mailto:support@profit.co) för att lägga till användarna i Profit.co SAML App-plattformen. Du kan inte använda enkel inloggning förrän du skapar och aktiverar användare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer den Profit.co SAML App-panelen på Åtkomstpanelen bör du automatiskt loggas in på den Profit.co SAML-app som du konfigurerar SSO för. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Profit.co SAML-app med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Profit.co SAML-app med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
