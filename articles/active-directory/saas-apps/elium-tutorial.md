---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Elium | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72791231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Elium

I den här självstudien får du lära dig hur du integrerar Elium med Azure Active Directory (Azure AD). När du integrerar Elium med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Elium.
* Gör att användarna automatiskt loggas in i Elium med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Elium enkel inloggning (SSO) aktiverat abonnemang.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Elium stöder **SP- och IDP**-initierad enkel inloggning
* Elium stöder **just-in-time**-användaretablering

## <a name="adding-elium-from-the-gallery"></a>Lägga till Elium från galleriet

För att konfigurera integreringen av Elium i Azure AD måste du lägga till Elium från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Elium** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Elium** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Konfigurera och testa en azure AD-inloggning för Elium

Konfigurera och testa Azure AD SSO med Elium med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Elium.

Så här konfigurerar och testar du Azure AD SSO med Elium:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Elium SSO](#configure-elium-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Elium-testanvändare](#create-elium-test-user)** - om du vill ha en motsvarighet till B.Simon i Elium som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<platform-domain>.elium.com/login/saml2/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du får dessa värden från den **SP-metadatafilen** som kan laddas ner på `https://<platform-domain>.elium.com/login/saml2/metadata`, som beskrivs senare i den här självstudien.

1. Elium-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Elium ansökan få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| ----------------|
    | e-post   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Det här är standardanspråk. **Endast e-anspråk krävs**. Även för JIT-etablering krävs endast e-anspråk. Andra anpassade anspråk kan variera från en kundplattform till en annan kundplattform.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser på avsnittet **Konfigurera Elium** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Elium.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan med program väljer du **Elium**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-elium-sso"></a>Konfigurera Elium SSO

1. Om du vill automatisera konfigurationen i Elium måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Ställ in Elium** kommer att leda dig till Elium ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Elium. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Elium manuellt öppnar du ett nytt webbläsarfönster och loggar in på eliumföretagets webbplats som administratör och utför följande steg:

1. Klicka på **Användarprofil** i det övre högra hörnet och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user1.png)

1. Välj fliken **Säkerhet**.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user2.png)

1. Rulla ned till avsnittet **Enkel inloggning (SSO)** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user3.png)

    a. Kopiera värdet för **Verify that SAML2 authentication works for your account** (Kontrollera att SAML2-autentiseringen fungerar för ditt konto) och klistra in det i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    > [!NOTE]
    > När du har konfigurerat SSO kan du alltid få åtkomst till standardsidan för fjärrinloggning via följande webbadress: `https://<platform_domain>/login/regular/login` 

    b. Markera kryssrutan **Aktivera SAML2-federation**.

    c. Markera kryssrutan **JIT-etablering**.

    d. Öppna **SP-metadata** genom att klicka på knappen **Ladda ned**.

    e. Sök efter **entityID** i **SP-metadatafilen**, kopiera **entityID**värdet och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. 

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user4.png)

    f. Sök efter **AssertionConsumerService** i **SP-metadatafilen**, kopiera **platsvärdet** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user5.png)

    g. Öppna den nedladdade metadatafilen från Azure-portalen i Anteckningar, kopiera innehållet och klistra in det i textrutan **IdP-metadata**.

    h. Klicka på **Spara**.

### <a name="create-elium-test-user"></a>Skapa Elium-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Elium. Elium stöder **just-in-time-etablering**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Elium skapas en ny när du försöker komma åt Elium.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [Elium-supportteamet](mailto:support@elium.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Elium-panelen på åtkomstpanelen bör du automatiskt loggas in i Elium-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Elium med Azure AD](https://aad.portal.azure.com/)