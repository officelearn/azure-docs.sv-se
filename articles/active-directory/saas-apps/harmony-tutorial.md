---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Harmony | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Harmony.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 332bf450-7242-4494-a4d9-057f6a24b858
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce15d89b0a67fd9b165b0b8b65537e72f5264eef
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harmony"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Harmony

I den här självstudien får du lära dig hur du integrerar Harmony med Azure Active Directory (Azure AD). När du integrerar Harmony med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Harmony.
* Gör att användarna automatiskt loggas in i Harmony med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En enhetlig inloggning (SSO) aktiverad prenumeration.

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Harmony stöder **IDP** initierad SSO
* När du har konfigurerat Harmony kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-harmony-from-the-gallery"></a>Lägga till Harmony från galleriet

Om du vill konfigurera integreringen av Harmony i Azure AD måste du lägga till Harmony från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Harmony** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Harmony** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harmony"></a>Konfigurera och testa en azure AD-inloggning för Harmony

Konfigurera och testa Azure AD SSO med Harmony med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Harmony.

Så här konfigurerar och testar du Azure AD SSO med Harmony:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Harmony SSO](#configure-harmony-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Harmony-testanvändare](#create-harmony-test-user)** – om du vill ha en motsvarighet till B.Simon i Harmony som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Harmony** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Harmony-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Harmony-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn |  Källattribut|
    | ------------ | --------- |
    | E-post | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera harmoni** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Harmony.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Harmony**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-harmony-sso"></a>Konfigurera Harmony SSO

Om du vill konfigurera enkel inloggning på **Harmony-sidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [Harmony-supportteamet](https://us.moodmedia.com/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-harmony-test-user"></a>Skapa Harmony-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Harmony. Arbeta med [Harmony-supportteamet](https://us.moodmedia.com/contact-us/) för att lägga till användarna på Harmony-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Harmoni på åtkomstpanelen bör du automatiskt loggas in på den Harmony som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Harmony med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Harmony med avancerad synlighet och avancerade kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

