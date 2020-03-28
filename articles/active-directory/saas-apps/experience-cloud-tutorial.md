---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Experience Cloud | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Experience Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 011e98ba-c452-40e1-983c-936f6a77ae2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d044a229764d0d6ce1d0f3b10b95d406ccd3dd5f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77371415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-experience-cloud"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Experience Cloud

I den här självstudien får du lära dig hur du integrerar Experience Cloud med Azure Active Directory (Azure AD). När du integrerar Experience Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Experience Cloud.
* Gör att användarna automatiskt loggas in på Experience Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Upplev Cloud single sign-on (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Experience Cloud stöder **SP och IDP** initierad SSO
* När du har konfigurerat Experience Cloud kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-experience-cloud-from-the-gallery"></a>Lägga till Experience Cloud från galleriet

Om du vill konfigurera integreringen av Experience Cloud i Azure AD måste du lägga till Experience Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Experience Cloud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Experience Cloud** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-experience-cloud"></a>Konfigurera och testa Azure AD enkel inloggning för Experience Cloud

Konfigurera och testa Azure AD SSO med Experience Cloud med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Experience Cloud.

Så här konfigurerar och testar du Azure AD SSO med Experience Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Experience Cloud SSO](#configure-experience-cloud-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Experience Cloud-testanvändare](#create-experience-cloud-test-user)** – om du vill ha en motsvarighet till B.Simon i Experience Cloud som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan Integrering av Experience **Cloud-program**hittar du avsnittet **Hantera** och väljer enkel inloggning . **Experience Cloud**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**
    
    |||
    |-|-|
    | `https://<cluster>.medallia.com/sso/<company>`|
    | `https://<cluster>.medallia.ca/sso/<company>`|
    | `https://<cluster>.medallia.eu/sso/<company>`|
    | `https://<cluster>.medallia.au/sso/<company>`|

    b. Skriv en URL med hjälp av följande mönster i textrutan **Svara URL:**

    |||
    |-|-|
    | `https://<cluster>.medallia.com/sso/<company>/logonSubmit.do`|
    | `https://<cluster>.medallia.ca/sso/<company>/logonSubmit.do`|
    | `https://<cluster>.medallia.eu/sso/<company>/logonSubmit.do`|
    | `https://<cluster>.medallia.au/sso/<company>/logonSubmit.do`|


1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**

    |||
    |-|-|
    | `https://<cluster>.medallia.com/sso/<company>`|
    | `https://<cluster>.medallia.ca/sso/<company>`|
    | `https://<cluster>.medallia.eu/sso/<company>`|
    | `https://<cluster>.medallia.au/sso/<company>`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Experience Cloud Client supportteam](mailto:support@medallia.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera experience cloud** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Experience Cloud.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Experience Cloud**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-experience-cloud-sso"></a>Konfigurera Experience Cloud SSO

Om du vill konfigurera enkel inloggning på **Experience Cloud-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portal till [Experience Cloud supportteam](mailto:support@medallia.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-experience-cloud-test-user"></a>Skapa Cloud-testanvändare för Experience Cloud

I det här avsnittet skapar du en användare som heter B.Simon i Experience Cloud. Arbeta med [Supportteamet](mailto:support@medallia.com) för Experience Cloud för att lägga till användarna i Experience Cloud-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Upplev moln på åtkomstpanelen bör du automatiskt loggas in i det Experience Cloud som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Experience Cloud med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)