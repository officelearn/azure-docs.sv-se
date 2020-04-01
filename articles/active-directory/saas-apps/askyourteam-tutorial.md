---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med AskYourTeam | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AskYourTeam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cef6764-de54-4920-b0ad-e560c214c72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403f39de1b7d226d9feeb33388c32f63271fdcd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med AskYourTeam

I den här självstudien får du lära dig hur du integrerar AskYourTeam med Azure Active Directory (Azure AD). När du integrerar AskYourTeam med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AskYourTeam.
* Gör att användarna automatiskt loggas in på AskYourTeam med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Fråga AourTeam enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* AskYourTeam stöder **SP och IDP** initierade SSO.
* När du har konfigurerat AskYourTeam kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Lägga till AskYourTeam från galleriet

Om du vill konfigurera integreringen av AskYourTeam i Azure AD måste du lägga till AskYourTeam från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **AskYourTeam** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Fråga DittTeam** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Konfigurera och testa en azure AD-inloggning för AskYourTeam

Konfigurera och testa Azure AD SSO med AskYourTeam med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i AskYourTeam.

Så här konfigurerar och testar du Azure AD SSO med AskYourTeam:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera AskYourTeam SSO](#configure-askyourteam-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa AskYourTeam-testanvändare](#create-askyourteam-test-user)** – om du vill ha en motsvarighet till B.Simon i AskYourTeam som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **AskYourTeam-programintegration** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL-värden som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera AskYourTeam** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AskYourTeam.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **AskYourTeam**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-askyourteam-sso"></a>Konfigurera AskYourTeam SSO

1. I ett annat webbläsarfönster loggar du in på AskYourTeams webbplats som administratör.

1. Klicka på **Min organisation**.

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Klicka på **Integrationer**.

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/configure1.png)

1. Klicka på **Redigera inställningar**.

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/configure2.png)

1. Gör följande på sidan **Redigera enkel inloggningsintegration:** 

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/configure3.png)

    a. Klistra in url-värdet **för inloggning** som du har kopierat från Azure-portalen i **SMS:en.**

    b. I **SMS:ens-ID-textrutan för SAML** klistrar du in azure **AD-identifierare** som du har kopierat från Azure-portalen.

    c. I textrutan **Utloggning av URL:en** klistrar du in **url-värdet för utloggning** som du har kopierat från Azure-portalen.

    d. Öppna det nedladdade **certifikatet (Base64)** från Azure-portalen i Anteckningar och klistra in innehållet i **SMS-signeringscertifikatet - Base64-textrutan.**

    > [!NOTE]
    > Du kan också ladda upp **XML-filen federationsmetadata** genom att klicka på alternativet **Välj fil.**

    e. Kopiera **url-värde för svar (Kontroll konsumenttjänst),** klistra in det här värdet i textrutan **Svara URL** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    f. Kopiera **värdet för inloggnings-URL,klistra** in det här värdet i textrutan **Logga in på URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    g. Klicka på **Spara**.

### <a name="create-askyourteam-test-user"></a>Skapa AskYourTeam-testanvändare

1. I ett annat webbläsarfönster loggar du in på AskYourTeams webbplats som administratör.

1. Klicka på **Min organisation**.

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Klicka på **Användare** och välj **Ny användare**.

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/user2.png)

1. Gör följande i avsnittet **Ny användare:**

    ![Konfigurera AskYourTeam](./media/askyourteam-tutorial/user3.png)

    1. Ange användarens förnamn i textrutan **Förnamn.**

    1. Ange användarens efternamn i textrutan **Efternamn.**

    1. I **textrutan E-post** anger du e-postadressen för användaren som B.Simon@contoso.com.

    1. Välj **rollen** för användaren enligt organisationens krav.

    1. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen AskYourTeam på åtkomstpanelen ska du automatiskt loggas in på den AskYourTeam som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova AskYourTeam med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
