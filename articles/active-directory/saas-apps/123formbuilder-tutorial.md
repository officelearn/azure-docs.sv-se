---
title: 'Självstudiekurs: Azure Active Directory-integrering med 123FormBuilder SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 123FormBuilder SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med 123FormBuilder SSO

I den här självstudien får du lära dig hur du integrerar 123FormBuilder SSO med Azure Active Directory (Azure AD). När du integrerar 123FormBuilder SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till 123FormBuilder SSO.
* Gör att användarna automatiskt loggas in på 123FormBuilder SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* 123FormBuilder SSO enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* 123FormBuilder SSO stöder **SP och IDP** initierade SSO.
* 123FormBuilder SSO stöder just in time-användaretablering. **Just In Time**
* När du har konfigurerat 123FormBuilder SSO kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Lägga till 123FormBuilder SSO från galleriet

Om du vill konfigurera integreringen av 123FormBuilder SSO i Azure AD måste du lägga till 123FormBuilder SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **123FormBuilder SSO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **123FormBuilder SSO** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Konfigurera och testa azure AD enkel inloggning för 123FormBuilder SSO

Konfigurera och testa Azure AD SSO med 123FormBuilder SSO med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i 123FormBuilder SSO.

Så här konfigurerar och testar du Azure AD SSO med 123FormBuilder SSO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera 123FormBuilder SSO](#configure-123formbuilder-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa 123FormBuilder SSO-testanvändare](#create-123formbuilder-sso-test-user)** – om du vill ha en motsvarighet till B.Simon i 123FormBuilder SSO som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **123FormBuilder SSO-programintegrering** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera de här värdena från faktiska URL:er och identifierare. Detta förklaras senare i självstudien.

1. På sidan **Installations enda inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera 123FormBuilder SSO.**

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till 123FormBuilder SSO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **123FormBuilder SSO**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-123formbuilder-sso"></a>Konfigurera 123FormBuilder SSO

1. Om du vill konfigurera enkel inloggning på **123FormBuilder SSO-sidan** går du till [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) och utför följande steg:

    ![Konfigurera enkel inloggning](./media/123formbuilder-tutorial/submit.png) 

    a. I textrutan **E-post** skriver du e-postadressen för användaren: `B.Simon@Contoso.com`.

    b. Klicka på **Ladda upp** och bläddra till den nedladdade metadata-XML-filen som du har laddat ned från Azure-portalen.

    c. Klicka på **SUBMIT FORM** (Skicka formulär).

2. I **Microsoft Azure AD - Single sign-on - Configure App Settings** (Microsoft Azure AD – Enkel inloggning – Konfigurera appinställningar) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/123formbuilder-tutorial/url3.png)

    a. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **IDENTIFIER** (Identifierare) för din instans och klistrar in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **REPLY URL** (Svars-URL) för din instans och klistrar in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. Om du vill konfigurera programmet i **SP-initierat läge** kopierar du värdet för **SIGN ON URL** (Inloggnings-URL) för din instans och klistrar in det i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

### <a name="create-123formbuilder-sso-test-user"></a>Skapa 123FormBuilder SSO-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i 123FormBuilder SSO. 123FormBuilder SSO stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i 123FormBuilder SSO skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SSO-panelen 123FormBuilder på åtkomstpanelen bör du automatiskt loggas in på SSO 123FormBuilder som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova 123FormBuilder SSO med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
