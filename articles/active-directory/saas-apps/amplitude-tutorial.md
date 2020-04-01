---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Amplitude | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d077da34a6e82ced957c4da1e6abf7a5e294e78
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72596261"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Amplitud

I den här självstudien får du lära dig hur du integrerar Amplitude med Azure Active Directory (Azure AD). När du integrerar Amplitude med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Amplitude.
* Gör att användarna automatiskt loggas in på Amplitude med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Amplitud enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Amplitude har stöd för **SP- och IDP**-initierad enkel inloggning
* Amplitude stöder **just-in-time**-användaretablering

## <a name="adding-amplitude-from-the-gallery"></a>Lägga till Amplitude från galleriet

För att konfigurera integrering av Amplitude i Azure AD, behöver du lägga till Amplitude från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Amplitud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Amplitud** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amplitude"></a>Konfigurera och testa en azure AD-inloggning för amplitud

Konfigurera och testa Azure AD SSO med Amplitude med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Amplitud.

Om du vill konfigurera och testa Azure AD SSO med Amplitud slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Amplitude SSO](#configure-amplitude-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa amplitudtestanvändare](#create-amplitude-test-user)** – om du vill ha en motsvarighet till B.Simon i Amplitude som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Identifierare:**`https://amplitude.com/saml/sso/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Senare i den här självstudien får du svars-URL-värdet.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://analytics.amplitude.com/sso`

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera amplitud** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Amplitude.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan med program väljer du **Amplitude**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-amplitude-sso"></a>Konfigurera amplitud SSO

1. Om du vill automatisera konfigurationen i Amplitud måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Ställ in Amplitude** kommer att leda dig till Amplitude ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Amplitude. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Amplitud manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats amplitud som administratör och utför följande steg:

1. Klicka på **Plan Admin** (avtalsadministratör) från det vänstra navigeringsfältet.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure1.png)

1. Välj **Microsoft Azure Active Directory-metadata** från **SSO-integreringen**.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure2.png)

1. I avsnittet **Konfigurera enkel inloggning** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure3.png)

    a. Öppna hämtade **Xml-metadata** från Azure-portalen i anteckningar och klistra in innehållet i textrutan för **Microsoft Azure Active Directory-metadata**.

    b. Kopiera värdet i **svars-URL:en (ACS)** och klistra in det i textrutan för **svars-URL** under **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Klicka på **Spara**

### <a name="create-amplitude-test-user"></a>Skapa Amplitude-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Amplitud. Amplitude stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Amplitude skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [Amplitude supportteam](https://amplitude.zendesk.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Amplitude-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Amplitude som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Amplitude med Azure AD](https://aad.portal.azure.com/)