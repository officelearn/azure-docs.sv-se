---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SumoLogic | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6d941c6efe42993b6bad7c556582831d179250
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SumoLogic

I den här självstudien får du lära dig hur du integrerar SumoLogic med Azure Active Directory (Azure AD). När du integrerar SumoLogic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SumoLogic.
* Aktivera dina användare så att de automatiskt loggas in på SumoLogic med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SumoLogic enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SumoLogic stöder **IDP-initierad** SSO

## <a name="adding-sumologic-from-the-gallery"></a>Lägga till SumoLogic från galleriet

Om du vill konfigurera integreringen av SumoLogic i Azure AD måste du lägga till SumoLogic från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SumoLogic** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SumoLogic** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Konfigurera och testa en azure AD-inloggning för SumoLogic

Konfigurera och testa Azure AD SSO med SumoLogic med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SumoLogic.

Så här konfigurerar och testar du Azure AD SSO med SumoLogic:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SumoLogic SSO](#configure-sumologic-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa SumoLogic-testanvändare](#create-sumologic-test-user)** - om du vill ha en motsvarighet till B.Simon i SumoLogic som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **SumoLogic**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | |
    |--|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [SumoLogic Client support team](https://www.sumologic.com/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SumoLogic-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sumoLogic ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    |  Namn | Källattribut |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Roller | user.assignedroles |

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) för att veta hur du konfigurerar **roll** i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera SumoLogic** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SumoLogic.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SumoLogic**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sumologic-sso"></a>Konfigurera SumoLogic SSO

1. I ett annat webbläsarfönster loggar du in på din SumoLogic-företagswebbplats som administratör.

1. Gå till **Hantera \> säkerhet**.

    ![Hantera](./media/sumologic-tutorial/ic778556.png "Hantera")

1. Klicka på **SAML**.

    ![Globala säkerhetsinställningar](./media/sumologic-tutorial/ic778557.png "Globala säkerhetsinställningar")

1. Välj **Azure AD**i listan Välj en konfiguration eller skapa **en ny** lista och klicka sedan på **Konfigurera**.

    ![Konfigurera SAML 2.0](./media/sumologic-tutorial/ic778558.png "Konfigurera SAML 2.0")

1. Gör följande i dialogrutan **Konfigurera SAML 2.0:**

    ![Konfigurera SAML 2.0](./media/sumologic-tutorial/ic778559.png "Konfigurera SAML 2.0")

    a. Skriv **Azure AD**i textrutan **Konfigurationsnamn** .

    b. Välj **Felsökningsläge**.

    c. Klistra in värdet för **Azure AD-identifierare**i textrutan **Utfärdare** som du har kopierat från Azure-portalen.

    d. Klistra in värdet för **inloggnings-URL**i textrutan **Authn Begär URL** , som du har kopierat från Azure-portalen.

    e. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in hela certifikatet i textrutan **X.509 Certifikat.**

    f. Som **e-postattribut**väljer du **Använd SAML-ämne**.  

    g. Välj **SP initierad inloggningskonfiguration**.

    h. Skriv **Azure** i textrutan **Inloggningssökväg** och klicka på **Spara**.

### <a name="create-sumologic-test-user"></a>Skapa SumoLogic-testanvändare

För att Azure AD-användare ska kunna logga in på SumoLogic måste de etableras till SumoLogic. När det gäller SumoLogic är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **SumoLogic-klient.**

1. Gå till **Hantera \> Användare**.

    ![Användare](./media/sumologic-tutorial/ic778561.png "Användare")

1. Klicka på **Lägg till**.

    ![Användare](./media/sumologic-tutorial/ic778562.png "Användare")

1. Gör följande i dialogrutan **Ny användare:**

    ![Ny användare](./media/sumologic-tutorial/ic778563.png "Ny användare")

    a. Skriv den relaterade informationen för det Azure AD-konto som du vill etablera i textrutorna **för förnamn,** **efternamn**och **e-post.**
  
    b. Välj en roll.
  
    c. Som **status**väljer du **Aktiv**.
  
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra SumoLogic-verktyg för att skapa användarkonton eller API:er som tillhandahålls av SumoLogic för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SumoLogic på åtkomstpanelen ska du automatiskt loggas in på den SumoLogic som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SumoLogic med Azure AD](https://aad.portal.azure.com/)