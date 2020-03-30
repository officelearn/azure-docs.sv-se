---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Contentful | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Contentful.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd218c61114c1e15009ace5a9a9bd7a536996e86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72968649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Contentful

I den här självstudien får du lära dig hur du integrerar Contentful med Azure Active Directory (Azure AD). När du integrerar Contentful med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Contentful.
* Gör att användarna automatiskt loggas in på Contentful med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Innehållsfylld enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Contentful stöder **SP och IDP** initierad SSO
* Contentful stöder just in time-användaretablering **Just In Time**

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde. Endast en instans kan konfigureras i en klient.

## <a name="adding-contentful-from-the-gallery"></a>Lägga till innehållsrik från galleriet

Om du vill konfigurera integreringen av Contentful i Azure AD måste du lägga till Contentful från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Innehållsfull i** sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Contentful** i resultaten och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Konfigurera och testa en azure AD-enkel inloggning för Contentful

Konfigurera och testa Azure AD SSO med Contentful med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Contentful.

Om du vill konfigurera och testa Azure AD SSO med Contentful slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Contentful SSO](#configure-contentful-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa innehållskänslig testanvändare](#create-contentful-test-user)** – om du vill ha en motsvarighet till B.Simon i Contentful som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På sidan **Innehållsfylld** programintegrering på **Azure-portalen**hittar du avsnittet **Hantera** och väljer enkel inloggning . [Azure portal](https://portal.azure.com/)
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    - I textrutan **Svara url** kopierar du ACS-url:en (Assertion Consumer Service) från installationssidan för SSO i Contentful. Det kommer att se ut så här:`https://be.contentful.com/sso/<organization_id>/consume`

1. Klicka på **Ange ytterligare webbadresser** och utför följande steg om du vill konfigurera programmet i **återupptastartat SP-läge:**

    - Kopiera samma ACS-url (Assertion Consumer Service) i textrutan **Sign-on URL.** Det kommer att se ut så här:`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en genom att kopiera ACS-url:en (Assertion Consumer Service) från installationssidan för SSO i Contentful.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera innehållskänsliga** kopierar du inloggningsadressen för att konfigurera Contentful SSO.

    ![Kopiera konfigurations-URL:er](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**, väljer **Användare**och väljer sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Contentful.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Contentful**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. Välj **B.Simon** i listan Användare i dialogrutan **Användare och grupper** och klicka sedan på knappen **Välj** längst ned på sidan.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på sidan.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-contentful-sso"></a>Konfigurera Contentful SSO

Följ dessa steg för att konfigurera enkel inloggning på **contentful** sidan.

1. I [Contentful](https://app.contentful.com)navigerar du till installationssidan för SSO i **Organisationsinställningar**.
1. Klicka på **Ställ in SSO**.
1. Kopiera och klistra in inloggningsadressen från avsnittet **Konfigurera innehållskänslig** i Azure AD.
1. Kopiera och klistra in certifikatet från base64-certifikatfilen som du hämtade från Azure AD.
1. Konfigurera ett SSO-namn för SP-initierad inloggning.
1. Klicka på **Aktivera SSO**.

Om det inte fungerar kan du kontakta [supportteamet för Contentful.](mailto:support@contentful.com)

### <a name="create-contentful-test-user"></a>Skapa innehållskänslig testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Contentful. Contentful stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Contentful skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Innehållsfull på åtkomstpanelen bör du automatiskt loggas in på den Contentful som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Contentful med Azure AD](https://aad.portal.azure.com/)
