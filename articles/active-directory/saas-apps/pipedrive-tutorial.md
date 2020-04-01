---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Pipedrive | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pipedrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3b97628d-9d0c-45ac-b8ef-7480cf7ec602
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 235169efdcd415df055885d325394c942a2a8d88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485652"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Pipedrive

I den här självstudien får du lära dig hur du integrerar Pipedrive med Azure Active Directory (Azure AD). När du integrerar Pipedrive med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Pipedrive.
* Gör att användarna automatiskt loggas in på Pipedrive med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Pipedrive enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Pipedrive stöder **SP och IDP** initierade SSO
* När du har konfigurerat Pipedrive SSO kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-pipedrive-from-the-gallery"></a>Lägga till Pipedrive från galleriet

Om du vill konfigurera integreringen av Pipedrive i Azure AD måste du lägga till Pipedrive från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Pipedrive** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Pipedrive** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Konfigurera och testa en azure AD-inloggning för Pipedrive

Konfigurera och testa Azure AD SSO med Pipedrive med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Pipedrive.

Så här konfigurerar och testar du Azure AD SSO med Pipedrive:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Pipedrive SSO](#configure-pipedrive-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Pipedrive-testanvändare](#create-pipedrive-test-user)** – om du vill ha en motsvarighet till B.Simon i Pipedrive som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Pipedrive** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Pipedrive Client support team](mailto:support@pipedrive.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Pipedrive-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar Pipedrive ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------ | --------- |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn och även kopiera **url:en url till Metadata för App Federation** och spara det på datorn.

    ![Länk för nedladdning av certifikatet](./media/pipedrive-tutorial/certificate-data.png)

1. Kopiera lämpliga webbadresser på avsnittet **Konfigurera Pipedrive** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pipedrive.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Pipedrive**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-pipedrive-sso"></a>Konfigurera Pipedrive SSO

1. Logga in på Pipedrives webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Användarprofil** och välj **Inställningar**.

    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/configure1.png)

1. Bläddra ned till säkerhetscentret och välj **Enkel inloggning**.

    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/configure2.png)

1. Gör följande på **SAML-konfigurationen för pipedrive-avsnittet:**

    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/configure3.png)

    a. I textrutan **Utfärdare** klistrar du in **url-värdet för App Federation Metadata,** som du har kopierat från Azure-portalen.

    b. I **textrutan Url för enkel inloggning(SSO)** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    c. I **textrutan Url för enkel utloggning(SLO)** klistrar du in **url-värdet för utloggning,** som du har kopierat från Azure-portalen.

    d. Öppna den nedladdade **certifikatfilen (Base64)** från Azure-portalen i Anteckningar i **x.509-certifikattextrutan** och kopiera innehållet i den och klistra in i **textrutan x.509 certifikat** och spara ändringar.

### <a name="create-pipedrive-test-user"></a>Skapa Pipedrive-testanvändare

1. Logga in på Pipedrives webbplats som administratör i ett annat webbläsarfönster.

1. Bläddra ned till företaget och välj **hantera användare**.

    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/user1.png)

1. Klicka på **Lägg till användare**.
    
    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/user2.png)

1. Gör följande i avsnittet **Hantera användare:**

    ![Pipedrive-konfiguration](./media/pipedrive-tutorial/user3.png)

    a. I **textrutan E-post** anger du e-postadressen till den användare som `B.Simon@contoso.com`.

    b. Ange användarens förnamn i textrutan **Förnamn.**

    c. Ange användarens efternamn i textrutan **Efternamn.**

    d. Klicka på **Bekräfta och bjud in användare**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Pipedrive-panelen på åtkomstpanelen ska du automatiskt loggas in på pipedrive som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Pipedrive med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)