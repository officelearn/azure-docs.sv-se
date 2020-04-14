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
ms.openlocfilehash: 05aed032eeb3a3cd925a718516ba9c8ffb87f65e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261116"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Nitro Productivity Suite

I den här självstudien får du lära dig hur du integrerar Nitro Productivity Suite med Azure Active Directory (Azure AD). När du integrerar Nitro Productivity Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nitro Productivity Suite.
* Gör det möjligt för användarna att automatiskt loggas in på Nitro Productivity Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Nitro Productivity Suite [Enterprise-prenumeration](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Nitro Productivity Suite stöder **SP och IDP** initierad SSO
* Nitro Productivity Suite stöder just **in time-användares** etablering
* När du har konfigurerat Nitro Productivity Suite kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-nitro-productivity-suite-from-the-gallery"></a>Lägga till Nitro Productivity Suite från galleriet

Om du vill konfigurera integreringen av Nitro Productivity Suite i Azure AD måste du lägga till Nitro Productivity Suite från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Nitro Productivity Suite** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Nitro Productivity Suite** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurera och testa en enda Azure AD-inloggning för Nitro Productivity Suite

Konfigurera och testa Azure AD SSO med Nitro Productivity Suite med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Nitro Productivity Suite.

Om du vill konfigurera och testa Azure AD SSO med Nitro Productivity Suite slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Nitro Productivity Suite SSO](#configure-nitro-productivity-suite-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Nitro Productivity Suite-testanvändare](#create-nitro-productivity-suite-test-user)** – om du vill ha en motsvarighet till B.Simon i Nitro Productivity Suite som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Nitro Productivity Suite-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På avsnittet **SAML-signeringscertifikat:**

    a. Hitta **certifikat (Base64)** och välj **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)
    
1. På avsnittet **Konfigurera Nitro Productivity Suite:**

    a. Klicka på kopieringsikonen bredvid **Inloggningsadressen**
    
    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
    
1. I [Nitro Admin-portalen](https://admin.gonitro.com/)hittar du avsnittet **Enkel inloggning** på sidan **Företagsinställningar** och klickar på knappen **Konfigurera SAML SSO.**

    a. Klistra in **inloggningsadressen** från steget ovan i fältet **Logga in URL.**
    
    a. Ladda upp **certifikatet (Base64)** från det tidigare steget ovan i fältet **X509 Signeringscertifikat.**
    
    a. Klicka på **Skicka**
    
    a. Klicka på **Aktivera enkel inloggning**


1. Gå tillbaka till [Azure-portalen,](https://portal.azure.com/)på sidan **Konfigurera enkel inloggning med SAML,** klicka på redigera/penna-ikonen för grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Kopiera och klistra in **fältet SAML Entitets-ID** i textrutan **Identifierare** från [Nitro Admin-portalen](https://admin.gonitro.com/). Det bör ha följande mönster:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Kopiera och klistra in **URL-fältet för ACS** i textrutan **Svara** URL från [Nitro Admin-portalen](https://admin.gonitro.com/). Det bör ha följande mönster:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://sso.gonitro.com/login`

1. Klicka på Spara.

1. Nitro Productivity Suite-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Dessutom förväntar sig Nitro Productivity Suite-programmet att ytterligare några attribut skickas tillbaka i SAML-svaret, som visas i följande tabell. Dessa attribut är förifyllda, men du kan granska dem enligt dina krav.
    
    | Namn  |  Källattribut|
    | ---------------| --------------- |
    | medarbetareAntal |  user.objectid |


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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nitro Productivity Suite.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Nitro Productivity **Suite**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-nitro-productivity-suite-sso"></a>Konfigurera Nitro Productivity Suite SSO

Om du vill konfigurera enkel inloggning på **Nitro Productivity Suite-sidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [Nitro Productivity Suite-supportteamet](https://www.gonitro.com/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-nitro-productivity-suite-test-user"></a>Skapa testanvändare för Nitro Productivity Suite

I det här avsnittet skapas en användare som heter Britta Simon i Nitro Productivity Suite. Nitro Productivity Suite stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Nitro Productivity Suite skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Nitro Productivity Suite på åtkomstpanelen bör du automatiskt loggas in på Nitro Productivity Suite som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Nitro Productivity Suite med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Nitro Productivity Suite med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

