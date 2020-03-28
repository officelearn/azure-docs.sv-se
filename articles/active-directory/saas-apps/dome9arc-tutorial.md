---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Check Point CloudGuard Dome9 Arc | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73885357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Check Point CloudGuard Dome9 Arc

I den här självstudien får du lära dig hur du integrerar Check Point CloudGuard Dome9 Arc med Azure Active Directory (Azure AD). När du integrerar Check Point CloudGuard Dome9 Arc med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Check Point CloudGuard Dome9 Arc.
* Gör att användarna automatiskt loggas in på Check Point CloudGuard Dome9 Arc med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Kontrollera Point CloudGuard Dome9 Arc enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Check Point CloudGuard Dome9 Arc stöder **SP och IDP** initierade SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Lägga till Check Point CloudGuard Dome9 Arc från galleriet

Om du vill konfigurera integreringen av Check Point CloudGuard Dome9 Arc i Azure AD måste du lägga till Check Point CloudGuard Dome9 Arc från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Check Point CloudGuard Dome9 Arc** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Check Point CloudGuard Dome9 Arc** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Konfigurera och testa azure AD enkel inloggning för Check Point CloudGuard Dome9 Arc

Konfigurera och testa Azure AD SSO med Check Point CloudGuard Dome9 Arc med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Check Point CloudGuard Dome9 Arc.

Så här konfigurerar och testar du Azure AD SSO med Check Point CloudGuard Dome9 Arc:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Check Point CloudGuard Dome9 Arc SSO](#configure-check-point-cloudguard-dome9-arc-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Check Point CloudGuard Dome9 Arc-testanvändare](#create-check-point-cloudguard-dome9-arc-test-user)** – om du vill ha en motsvarighet till B.Simon i Check Point CloudGuard Dome9 Arc som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Check Point CloudGuard Dome9** **Arc-programintegration**och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Identifierare:**`https://secure.dome9.com/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Du får `<company name>` värdet från **avsnittet Konfigurera Check Point CloudGuard Dome9 Arc SSO,** som förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Check Point CloudGuard Dome9 Arc-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Check Point CloudGuard Dome9 Arc-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn |  Källattribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Klicka [här](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) om du vill veta hur du skapar roller i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Check Point CloudGuard Dome9 Arc.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Check Point CloudGuard Dome9 Arc.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Check Point **CloudGuard Dome9 Arc**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Konfigurera Check Point CloudGuard Dome9 Arc SSO

1. Om du vill automatisera konfigurationen i Check Point CloudGuard Dome9 Arc måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Check Point CloudGuard Dome9 Arc** leder dig till Check Point CloudGuard Dome9 Arc-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Check Point CloudGuard Dome9 Arc. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Check Point CloudGuard Dome9 Arc manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Check Point CloudGuard Dome9 Arc-företagswebbplats som administratör och utför följande steg:

2. Klicka på **profilinställningarna** uppe i högra hörnet och klicka sedan på **Account Settings** (Kontoinställningar). 

    ![Check Point CloudGuard Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure1.png)

3. Gå till **SSO** och klicka sedan på **ENABLE**.

    ![Check Point CloudGuard Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure2.png)

4. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Check Point CloudGuard Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i textrutan **Account ID** (Konto-ID). Det här värdet ska användas i **url:en svara** och **logga in** som nämns i grundläggande **SAML-konfiguration** i Azure-portalen.

    b. I textrutan **Utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat formuläret Azure-portalen.

    c. I textrutan **Idp endpoint url** (url till Idp-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).

    e. Klicka på **Spara**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Skapa Check Point CloudGuard Dome9 Arc testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Check Point CloudGuard Dome9 Arc måste de etableras i programmet. Check Point CloudGuard Dome9 Arc stöder just-in-time-etablering, men för att det ska fungera korrekt måste användaren välja viss **roll** och tilldela samma till användaren.

   >[!Note]
   >För **rollskapande** och annan information kontakta [Check Point CloudGuard Dome9 Arc Client support team](mailto:Dome9@checkpoint.com).

**Utför följande steg om du vill etablera ett användarkonto manuellt:**

1. Logga in på din Check Point CloudGuard Dome9 Arc-företagswebbplats som administratör.

2. Klicka på **Users & Roles** (Användare och roller) och sedan på **Users** (Användare).

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user2.png)

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren, till exempel B.Simon@contoso.com.

    b. Skriv förnamn för användaren som B i textrutan **Förnamn.**

    c. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    d. Välj **På** för **användaren**.

    e. Klicka på **SKAPA**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Check Point CloudGuard Dome9 Arc på åtkomstpanelen ska du automatiskt loggas in på checkpunkten CloudGuard Dome9-bågen som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Check Point CloudGuard Dome9 Arc med Azure AD](https://aad.portal.azure.com/)