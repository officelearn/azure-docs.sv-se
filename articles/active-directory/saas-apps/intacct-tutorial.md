---
title: 'Självstudiekurs: Azure Active Directory-integrering med Sage Intacct | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73570502"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Självstudiekurs: Integrera Salvia Intacct med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Sage Intacct med Azure Active Directory (Azure AD). När du integrerar Sage Intacct med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Sage Intacct.
* Gör att användarna automatiskt loggas in på Sage Intacct med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Sage Intacct enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Sage Intacct stöder **IDP-initierad** SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Lägga till Sage Intacct från galleriet

Om du vill konfigurera integreringen av Sage Intacct i Azure AD måste du lägga till Sage Intacct från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Salvia Intacct** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Salvia Intacct** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Konfigurera och testa en enkel Azure AD-inloggning för Salvia Intacct

Konfigurera och testa Azure AD SSO med Sage Intacct med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sage Intacct.

Så här konfigurerar och testar du Azure AD SSO med Sage Intacct:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
2. **[Konfigurera Sage Intacct SSO](#configure-sage-intacct-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Sage Intacct-testanvändare](#create-sage-intacct-test-user)** – om du vill ha en motsvarighet till B.Simon i Sage Intacct som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Sage Intacct-program** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL i textrutan **Svara URL:**`https://www.intacct.com/ia/acct/sso_response.phtml`

1. Programmet Salvia Intacct förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon om du vill öppna dialogrutan Användarattribut..

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Sage Intacct-programmet att få fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn  |  Källattribut|
    | ---------------| --------------- |
    | Företagsnamn | **Företag id för salvia Intacct** |
    | namn | Värdet ska vara samma som **användar-ID:n**För Sage Intacct , som du anger i **avsnittet Skapa salvia intacct-testanvändare**, som förklaras senare i självstudien |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Värdet ska vara samma som avsnittet Sage Intacct **Federated SSO User ID**, som du anger i **avsnittet Skapa Salvia Intacct-testanvändare**, som förklaras senare i självstudien |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera salvia intacct.**

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

I det här avsnittet aktiverar du B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Sage Intacct.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Sage Intacct i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sage-intacct-sso"></a>Konfigurera Sage Intacct SSO

1. I ett annat webbläsarfönster loggar du in på företagets webbplats Sage Intacct som administratör.

1. Klicka på fliken **Företag** och sedan på **Företagsinformation**.

    ![Företag](./media/intacct-tutorial/ic790037.png "Företag")

1. Klicka på fliken **Säkerhet** och sedan på **Redigera**.

    ![Säkerhet](./media/intacct-tutorial/ic790038.png "Säkerhet")

1. I avsnittet **Enkel inloggning (SSO)** utför du följande steg:

    ![Enkel skylt på](./media/intacct-tutorial/ic790039.png "enkel skylt på")

    a. Välj **Aktivera enkel inloggning .**

    b. Som **identitetsprovidertyp**väljer du **SAML 2.0**.

    c. Klistra in värdet för **Azure AD-identifierare**i textrutan **Utfärdare** url som du har kopierat från Azure-portalen.

    d. I textrutan **Inloggnings-URL** klistrar du in värdet **för inloggnings-URL**, som du har kopierat från Azure-portalen.

    e. Öppna **ditt bas-64-kodade** certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i **rutan Certifikat.**

    f. Klicka på **Spara**.

### <a name="create-sage-intacct-test-user"></a>Skapa Sage Intacct-testanvändare

Om du vill konfigurera Azure AD-användare så att de kan logga in på Salvia Intacct måste de etableras i Sage Intacct. För Salvia Intacct är etablering en manuell aktivitet.

**Så här etablerar du användarkonton:**

1. Logga in på din **Sage Intacct-klient.**

1. Klicka på fliken **Företag** och sedan på **Användare**.

    ![Användare](./media/intacct-tutorial/ic790041.png "Användare")

1. Klicka på fliken **Lägg till.**

    ![Lägg till](./media/intacct-tutorial/ic790042.png "Lägg till")

1. Gör följande i avsnittet **Användarinformation:**

    ![Användarinformation](./media/intacct-tutorial/ic790043.png "Användarinformation")

    a. Ange **användar-ID,** **efternamn,** **förnamn,** **e-postadress,** **titel**och **telefon** för ett Azure AD-konto som du vill etablera i avsnittet **Användarinformation.**

    > [!NOTE]
    > Kontrollera att **användar-ID:n** i ovanstående skärmbild och **källattributvärdet** som mappas med **namnattributet** i avsnittet **Användarattribut** i Azure-portalen ska vara detsamma.

    b. Välj **administratörsbehörighet** för ett Azure AD-konto som du vill etablera.

    c. Klicka på **Spara**. 
    
    d. Azure AD-kontoinnehavaren tar emot ett e-postmeddelande och följer en länk för att bekräfta sitt konto innan det blir aktivt.

1. Klicka på Fliken Enkel inloggning och se till att det **federerade SSO-användar-ID:t** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` i skärmbilden nedan och värdet för **källattribut** som mappas med avsnittet i **avsnittet Användarattribut** i Azure-portalen ska vara detsamma. **Single sign-on**

    ![Användarinformation](./media/intacct-tutorial/ic790044.png "Användarinformation")

> [!NOTE]
> Om du vill etablera Azure AD-användarkonton kan du använda andra verktyg för att skapa Sage Intacct-användarkonton eller API:er som tillhandahålls av Sage Intacct.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Salvia Intacct på åtkomstpanelen ska du automatiskt loggas in på den Sage Intacct som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

