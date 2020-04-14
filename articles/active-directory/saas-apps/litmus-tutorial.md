---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Litmus | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3b570512-f5b2-490e-8e72-b530c0b53956
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef0395f5898cbd566d31e39f80a165ae767cd31f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Litmus

I den här självstudien får du lära dig hur du integrerar Litmus med Azure Active Directory (Azure AD). När du integrerar Litmus med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Litmus.
* Gör att användarna automatiskt loggas in på Litmus med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Litmus enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Litmus stöder **SP och IDP** initierade SSO
* När du har konfigurerat Litmus kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-litmus-from-the-gallery"></a>Lägga till Lackmus från galleriet

Om du vill konfigurera integreringen av Litmus i Azure AD måste du lägga till Litmus från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Litmus** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Litmus** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Konfigurera och testa en azure AD-inloggning för Litmus

Konfigurera och testa Azure AD SSO med Litmus med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Litmus.

Om du vill konfigurera och testa Azure AD SSO med Litmus slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Litmus SSO](#configure-litmus-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Litmus-testanvändare](#create-litmus-test-user)** - om du vill ha en motsvarighet till B.Simon i Litmus som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://litmus.com/sessions/new`

1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Lackmus** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Litmus.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Litmus**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-litmus-sso"></a>Konfigurera Litmus SSO

1. I ett annat webbläsarfönster loggar du in på Litmus-programmet som administratör.

1. Klicka på **säkerheten** från den vänstra navigeringspanelen.

    ![Konfiguration av lackmus](./media/litmus-tutorial/security-img.png)

1. Gör följande i avsnittet **Konfigurera SAML-autentisering:**

    ![Konfiguration av lackmus](./media/litmus-tutorial/configure1.png)

    a. Slå på **växelknappen Aktivera SAML.**

    b. Välj **Allmänt** för providern.

    c. Ange namnet på **identitetsproviderns namn**. för ex. `Azure AD`

1. Utför följande steg:

    ![Konfiguration av lackmus](./media/litmus-tutorial/configure3.png)

    a. I textrutan **SAML 2.0 Endpoint(HTTP)** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    b. Öppna nedladdad **certifikatfil** från Azure-portalen till Anteckningar och klistra in innehållet i **textrutan X.509-certifikat.**

    c. Klicka på **Spara SAML-inställningar**.

### <a name="create-litmus-test-user"></a>Skapa litmustestanvändare

1. I ett annat webbläsarfönster loggar du in på Litmus-programmet som administratör.

1. Klicka på **kontona** från den vänstra navigeringspanelen.

    ![Konfiguration av lackmus](./media/litmus-tutorial/accounts-img.png)

1. Klicka på **Fliken Lägg till ny användare.**

    ![Konfiguration av lackmus](./media/litmus-tutorial/add-new-user.png)

1. Gör följande i avsnittet **Lägg till användare:**

    ![Konfiguration av lackmus](./media/litmus-tutorial/user-profile.png)

    a. I **textrutan e-post** anger du e-postadressen till den användare som**B.Simon@contoso.com**

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **B**.

    c. Ange efternamnet för användaren som **Simon**i textrutan **Efternamn** .

    d. Klicka på **Skapa användare**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Lackmus på åtkomstpanelen ska du automatiskt loggas in på den Lackmus som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Litmus med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Litmus med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
