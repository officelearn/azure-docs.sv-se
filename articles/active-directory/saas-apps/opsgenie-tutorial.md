---
title: 'Självstudiekurs: Azure Active Directory-integrering med OpsGenie | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 3bd559b90a4df5573a44b895ce3b097864d1add7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585601"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (Azure AD). När du integrerar OpsGenie med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OpsGenie.
* Gör att användarna automatiskt loggas in på OpsGenie med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* OpsGenie enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* OpsGenie stöder **IDP** initierad SSO
* När du har konfigurerat OpsGenie kan du framtvinga sessionskontroll, vilket skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>Lägga till OpsGenie från galleriet

Om du vill konfigurera integreringen av OpsGenie i Azure AD måste du lägga till OpsGenie från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **OpsGenie** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **OpsGenie** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Konfigurera och testa en azure AD-inloggning för OpsGenie

Konfigurera och testa Azure AD SSO med OpsGenie med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i OpsGenie.

Så här konfigurerar och testar du Azure AD SSO med OpsGenie:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera OpsGenie SSO](#configure-opsgenie-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa OpsGenie-testanvändare](#create-opsgenie-test-user)** – om du vill ha en motsvarighet till B.Simon i OpsGenie som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **OpsGenie** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med url:en för faktisk identifierare och svar, vilket förklaras senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera OpsGenie.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OpsGenie.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **OpsGenie**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-opsgenie-sso"></a>Konfigurera OpsGenie SSO

1. Öppna en annan webbläsarinstans och logga sedan in på OpsGenie som administratör.

2. Klicka på **Inställningar**och sedan på fliken **Enkel inloggning.**
   
    ![OpsGenie enkel inloggning](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Om du vill aktivera SSO väljer du **Aktiverad**.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Klicka på fliken **Azure Active Directory** i avsnittet **Provider.**
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Gör följande på dialogsidan i Azure Active Directory:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopiera **app-ID URI-värdet** och klistra in det i textrutan **Identifierare (Entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    a. Kopiera **värdet svara på URL** och klistra in det i textrutan **Svara på URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    a. I **sms-textrutan SAML 2.0** klistrar du in **url-värde**för inloggning som du har kopierat från Azure-portalen.
    
    b. I **metadataadressen:** textrutan klistrar du in **url-värdet för App Federation Metadata** som du har kopierat från Azure-portalen.
    
    c. Om du vill aktivera SSO aktiverar du växlingsknappen **Aktivera enkel inloggning.**

    d. Klicka på **Använd SSO-inställningar**.

### <a name="create-opsgenie-test-user"></a>Skapa OpsGenie-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i OpsGenie. 

1. Logga in på din OpsGenie-klient som administratör i ett webbläsarfönster.

2. Navigera till listan Användare genom att klicka på **Användare** på den vänstra panelen.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klicka på **Lägg till användare**.

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Skriv e-postadressen till B.Simon i Azure Active Directory i **textrutan e-post.**
   
    b. Skriv **B.Simon**i textrutan **Fullständigt namn** .
   
    c. Klicka på **Spara**. 

> [!NOTE]
> B.Simon får ett e-postmeddelande med instruktioner för hur du konfigurerar sin profil.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på OpsGenie-panelen på åtkomstpanelen ska du automatiskt loggas in på opsGenie som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova OpsGenie med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)