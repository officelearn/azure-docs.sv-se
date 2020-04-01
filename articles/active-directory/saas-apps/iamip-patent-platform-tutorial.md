---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med IamIP Patent Platform | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IamIP Patent Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med IamIP Patent Platform

I den här självstudien får du lära dig hur du integrerar IamIP Patent Platform med Azure Active Directory (Azure AD). När du integrerar IamIP Patent Platform med Azure AD kan du:

* Använd Azure AD för att styra vem som kan komma åt IamIP Patent Platform.
* Gör det möjligt för användarna att automatiskt loggas in på IamIP Patent Platform med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En IamIP Patent Platform-prenumeration med enkel inloggning (SSO) aktiverad.

## <a name="tutorial-description"></a>Beskrivning av självstudiekurs

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

IamIP Patent Platform stödjer SP-initierad och IDP-initierad SSO.

När du har konfigurerat IamIP Patent Platform kan du genomdriva sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Lägg till IamIP Patent Platform från galleriet

Om du vill konfigurera integreringen av IamIP Patent Platform i Azure AD måste du lägga till IamIP Patent Platform från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller med ett personligt Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **Enterprise-program** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **IamIP Patent Platform** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **IamIP Patent Platform** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Konfigurera och testa Azure AD SSO för IamIP Patent Platform

Du konfigurerar och testar Azure AD SSO med IamIP Patent Platform med hjälp av en testanvändare som heter B.Simon. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och motsvarande användare i IamIP Patent Platform.

Om du vill konfigurera och testa Azure AD SSO med IamIP Patent Platform vidtar du följande åtgärder på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
    * **[Bevilja åtkomst till testanvändaren](#grant-access-to-the-test-user)** så att användaren kan använda azure AD enkel inloggning.

1. **[Konfigurera IamIP Patent Platform SSO](#configure-iamip-patent-platform-sso)** på applikationssidan.
    * **[Skapa en IamIP Patent Platform testanvändare](#create-iamip-patent-platform-test-user)** som en motsvarighet till Azure AD representation av användaren.

1. **[Testa SSO](#test-sso)** för att kontrollera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. Välj [Azure portal](https://portal.azure.com/)en enda inloggning på sidan IamIP Patent Platform-programintegrering på sidan **IamIP Patent** **Manage** **Platform-program.**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennknappen för Grundläggande **SAML-konfiguration** för att redigera inställningarna:

   ![Pennknapp för grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du har en metadatafil för Service Provider och vill konfigurera SSO i IDP-initierat läge i avsnittet **Grundläggande SAML-konfiguration** gör du så här:

    a. Välj **Ladda upp metadatafil:**

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Markera mappknappen, markera metadatafilen och välj sedan **Ladda upp:**

    ![Knappar för mapp och uppladdning](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls **URL-värdena identifierare** och **svara** automatiskt i avsnittet **Grundläggande SAML-konfiguration:**

    ![Url-värden för identifierare och svara](common/idp-intiated.png)

    > [!Note]
    > Om **URL-värdena identifierare** och **Svara** inte fylls i automatiskt anger du värdena manuellt enligt dina krav.

1. Välj **Ange ytterligare webbadresser** och slutför följande steg om du vill konfigurera programmet i SP-initierat läge:

    I rutan **Inloggnings-URL** anger du **https:\//patents.iamip.com/login-user**.

1. På sidan **Konfigurera enkel inloggning med SAML** väljer du länken Hämta för certifikat **(Raw)** i avsnittet **Saml-signeringscertifikat** för att hämta certifikatet och spara det på datorn: **Download**

    ![Länk för nedladdning av certifikat](common/certificateraw.png)

1. I avsnittet **Konfigurera IamIP Patent Platform** kopierar du lämplig webbadress eller webbadresser, baserat på dina krav:

    ![Kopiera konfigurations-URL:er](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet B.Simon i Azure-portalen.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen**. Välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i **egenskaperna Användare:**
   1. Ange **B.Simon**i rutan **Namn** .  
   1. Ange \<användarnamn>@\<companydomain> i rutan **Användarnamn.** \<förlängning>. Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösenord**och skriv sedan ned värdet som visas i rutan **Lösenord.**
   1. Välj **Skapa**.

### <a name="grant-access-to-the-test-user"></a>Bevilja åtkomst till testanvändaren

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att ge användaren åtkomst till IamIP Patent Platform.

1. I Azure-portalen väljer du **Enterprise-program**och väljer sedan **Alla program**.
1. Välj **IamIP Patent Platform**i listan över ansökningar .
1. På appens översiktssida väljer du **Användare och grupper**i avsnittet **Hantera:**

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning:**

    ![Välj Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan **Användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurera IamIP Patent Platform SSO

Om du vill konfigurera enkel inloggning på IamIP Patent Platform-sidan måste du skicka det nedladdade råcertifikatet och lämpliga webbadresser som du kopierade från Azure-portalen till [IamIP Patent Platform supportteam](mailto:info@iamip.com). De konfigurerar SAML SSO-anslutningen så att den är korrekt på båda sidor.

### <a name="create-iamip-patent-platform-test-user"></a>Skapa IamIP Patent Platform test användare

Arbeta med [IamIP Patent Platform supportteam](mailto:info@iamip.com) för att lägga till en användare som heter B.Simon i IamIP Patent Platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av Åtkomstpanelen.

När du väljer panelen IamIP Patent Platform i Access Panel ska du automatiskt loggas in på den IamIP Patent Platform-instans som du ställer in SSO för. Mer information om Åtkomstpanelen finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova IamIP Patent Platform med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
