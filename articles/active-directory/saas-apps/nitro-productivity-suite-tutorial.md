---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Nitro Productivity Suite | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nitro Productivity Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Nitro Productivity Suite

I den här självstudien får du lära dig hur du integrerar Nitro Productivity Suite med Azure Active Directory (Azure AD). När du integrerar Nitro Productivity Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nitro Productivity Suite.
* Gör det möjligt för användarna att automatiskt loggas in på Nitro Productivity Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Nitro Productivity Suite [Enterprise-prenumeration](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Nitro Productivity Suite stöder **SP** och **IDP** initierade SSO.
* Nitro Productivity Suite stöder just in time-användaretablering. **Just In Time**
* När du har konfigurerat Nitro Productivity Suite kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Lägg till Nitro Productivity Suite från galleriet

Om du vill konfigurera integreringen av Nitro Productivity Suite i Azure AD måste du lägga till Nitro Productivity Suite från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj Azure Active **Directory**i den vänstra rutan .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Nitro Productivity Suite** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Nitro Productivity Suite** bland resultaten och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurera och testa en enda Azure AD-inloggning för Nitro Productivity Suite

Konfigurera och testa Azure AD SSO med Nitro Productivity Suite med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Nitro Productivity Suite.

Om du vill konfigurera och testa Azure AD SSO med Nitro Productivity Suite slutför du följande byggblock:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. [Konfigurera Nitro Productivity Suite SSO](#configure-nitro-productivity-suite-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. [Skapa en Nitro Productivity Suite-testanvändare](#create-a-nitro-productivity-suite-test-user) för att ha en motsvarighet till B.Simon i Nitro Productivity Suite, kopplad till Azure AD-representationen för användaren.
1. [Testa SSO](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Nitro Productivity Suite-programintegrering på **Nitro Productivity Suite** [Azure-portalen.](https://portal.azure.com/) **Manage** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. Leta reda på **certifikat (Base64)** i avsnittet **SAML-signeringscertifikat** . Välj **Hämta** om du vill hämta certifikatet och spara det på datorn.

    ![Skärmbild av avsnittet SAML-signeringscertifikat, med länken Hämta markerad](common/certificatebase64.png)
    
1. I avsnittet **Konfigurera Nitro Productivity Suite** väljer du kopieringsikonen bredvid **Inloggningsadressen**.
    
    ![Skärmbild av avsnittet Konfigurera Nitro Productivity Suite, med webbadresser och kopieringsikoner markerade](common/copy-configuration-urls.png)
    
1. Leta reda [på](https://admin.gonitro.com/)avsnittet Enkel **inloggning** på sidan Inställningar för Företag på sidan **Företagsinställningar.** Välj **Konfigurera SAML SSO**.

    a. Klistra in **inloggningsadressen** från föregående steg i fältet **Logga in URL.**
    
    b. Ladda upp **certifikatet (Base64)** från det tidigare steget i fältet **X509 Signeringscertifikat.**
    
    c. Välj **Skicka**.
    
    d. Välj **Aktivera enkel inloggning**.


1. Gå tillbaka till [Azure-portalen](https://portal.azure.com/). På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Kopiera och klistra in **fältet SAML Entitets-ID** i textrutan **Identifierare** från [Nitro Admin-portalen](https://admin.gonitro.com/). Det bör ha följande mönster:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Kopiera och klistra in **URL-fältet för ACS** i textrutan **Svara** URL från [Nitro Admin-portalen](https://admin.gonitro.com/). Det bör ha följande mönster:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Välj **Ange ytterligare webbadresser**och utför följande steg om du vill konfigurera programmet i **återupptan** av SP-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://sso.gonitro.com/login`

1. Välj **Spara**.

1. Nitro Productivity Suite-programmet förväntar sig att SAML-påståendena är i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Skärmbild av standardattribut](common/default-attributes.png)

1. Förutom de föregående attributen förväntar sig Nitro Productivity Suite-programmet att ytterligare några attribut skickas tillbaka i SAML-svaret. Dessa attribut är förifyllda, men du kan granska dem enligt dina krav.
    
    | Namn  |  Källattribut|
    | ---------------| --------------- |
    | medarbetareAntal |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** > .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned lösenordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Nitro Productivity Suite.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj Nitro Productivity **Suite**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i listan över användare **och grupper.** Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-nitro-productivity-suite-sso"></a>Konfigurera Nitro Productivity Suite SSO

Om du vill konfigurera enkel inloggning på Nitro Productivity Suite-sidan skickar du det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [Nitro Productivity Suite-supportteamet](https://www.gonitro.com/support). Supportteamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Skapa en nitro produktivitetssvit testanvändare

Nitro Productivity Suite stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns ingen ytterligare åtgärd för dig att vidta. Om en användare inte redan finns i Nitro Productivity Suite skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer panelen Nitro Productivity Suite på Åtkomstpanelen loggas du automatiskt in på Nitro Productivity Suite som du konfigurerar SSO för. Mer information finns [i Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Nitro Productivity Suite med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Nitro Productivity Suite med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

