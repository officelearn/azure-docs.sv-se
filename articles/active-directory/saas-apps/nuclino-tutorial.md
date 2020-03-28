---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Nuclino | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a75869c257e6b875a00036218b05db5521e8d0b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72532964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Nuclino

I den här självstudien får du lära dig hur du integrerar Nuclino med Azure Active Directory (Azure AD). När du integrerar Nuclino med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nuclino.
* Gör att användarna automatiskt loggas in på Nuclino med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Nuclino enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Nuclino stöder **SP och IDP** initierade SSO
* Nuclino stöder just in time-användaretablering **Just In Time**

## <a name="adding-nuclino-from-the-gallery"></a>Lägga till Nuclino från galleriet

Om du vill konfigurera integreringen av Nuclino i Azure AD måste du lägga till Nuclino från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Nuclino** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Nuclino** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Konfigurera och testa en azure AD-inloggning för Nuclino

Konfigurera och testa Azure AD SSO med Nuclino med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Nuclino.

Så här konfigurerar och testar du Azure AD SSO med Nuclino:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Nuclino SSO](#configure-nuclino-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Nuclino-testanvändare](#create-nuclino-test-user)** – om du vill ha en motsvarighet till B.Simon i Nuclino som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Nuclino** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med url:en för faktisk identifierare och svar från avsnittet **Autentisering,** vilket förklaras senare i den här självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Nuclino Client supportteam](mailto:contact@nuclino.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Nuclino-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

7. Utöver ovanstående förväntar sig Nuclino ansökan få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Nuclino** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nuclino.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Nuclino**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-nuclino-sso"></a>Konfigurera Nuclino SSO

1. Om du vill automatisera konfigurationen i Nuclino måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Ställ in Nuclino** kommer att hänvisa dig till Nuclino ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Nuclino. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Nuclino manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Nuclino-företagswebbplats som administratör och utför följande steg:

4. Klicka på **ICON**.

    ![Nuclino konfiguration](./media/nuclino-tutorial/configure1.png)

5. Klicka på **Azure AD SSO** och välj **Gruppinställningar** i listrutan.

    ![Nuclino konfiguration](./media/nuclino-tutorial/configure2.png)

6. Välj **Autentisering** från det vänstra navigeringsfönstret.

    ![Nuclino konfiguration](./media/nuclino-tutorial/configure3.png)

7. Gör följande i avsnittet **Autentisering:**

    ![Nuclino konfiguration](./media/nuclino-tutorial/configure4.png)

    a. Välj **SAML-baserad enkel inloggning (SSO)**.

    b. Kopiera **ACS-URL:en (Du måste kopiera och klistra in det här till SSO-leverantören)** och klistra in det i textrutan **Svara på URL** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    c. Kopiera **entitets-ID (Du måste kopiera och klistra in det här till SSO-providern)** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    d. I textrutan **SSO URL** klistrar du in värdet **för inloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **Entitets-ID** klistrar du in **azure AD-identifierare** som du har kopierat från Azure-portalen.

    f. Öppna den nedladdade **Certificate(Base64)**-filen i Anteckningar. Kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **Offentligt certifikat.**

    g. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-nuclino-test-user"></a>Skapa Nuclino-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Nuclino. Nuclino stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Nuclino skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [Nuclinos supportteam](mailto:contact@nuclino.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Nuclino-panelen i åtkomstpanelen ska du automatiskt loggas in på den Nuclino som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Nuclino med Azure AD](https://aad.portal.azure.com/)

