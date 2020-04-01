---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Netskope Administrator Console | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netskope Administrator Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c38900d4ded3d2ee08245674bda90d96226c1eb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Netskope Administrator Console

I den här självstudien får du lära dig hur du integrerar Netskope Administrator Console med Azure Active Directory (Azure AD). När du integrerar Netskope Administrator Console med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netskope Administrator Console.
* Gör att användarna automatiskt loggas in på Netskope Administrator Console med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Netskope Administrator Console enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Netskope Administrator Console stöder **SP och IDP** initierad SSO

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Lägga till Netskope Administrator Console från galleriet

Om du vill konfigurera integreringen av Netskope Administrator Console i Azure AD måste du lägga till Netskope Administrator Console från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Netskope Administrator Console** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Netskope Administrator Console** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Konfigurera och testa en azure AD-inloggning för Netskope Administrator Console

Konfigurera och testa Azure AD SSO med Netskope Administrator Console med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Netskope Administrator Console.

Så här konfigurerar och testar du Azure AD SSO med Netskope Administrator Console:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Netskope Administrator Console SSO](#configure-netskope-administrator-console-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Netskope Administrator Console testanvändare](#create-netskope-administrator-console-test-user)** - att ha en motsvarighet till B.Simon i Netskope Administratörskonsol som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj enkel inloggning på sidan **Netskope Administrator** **Manage** **Console-programintegration.**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`<OrgKey>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Du kommer att få dessa värden förklaras senare i handledningen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<tenantname>.goskope.com`

    > [!NOTE]
    > URL-värdena för inloggning är inte verkliga. Uppdatera url-värdet för inloggning med den faktiska inloggnings-URL:en. Kontakta [Netskope Administrator Console Client support team](mailto:support@netskope.com) för att få inloggnings-URL-värde. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Netskope Administrator Console-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig Netskope Administrator Console ansökan få fler attribut som ska skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------| --------- |
    | admin-roll | user.assignedroles |

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) om du vill veta hur du skapar roller i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Netskope Administrator Console** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Netskope Administrator Console.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Netskope Administrator Console**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurera Netskope Administrator Console SSO

1. Öppna en ny flik i webbläsaren och logga in på företagets webbplats för Netskope Administrator Console som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigeringsfönstret.

    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicka på fliken **Administration.**

    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klicka på fliken **SSO.**

    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Gör följande i avsnittet **Nätverksinställningar:**
    
    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Kopiera **URL-värdet för kontrollhantering** och klistra in det i textrutan **Svara url** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    b. Kopiera **entitets-ID-värde för tjänstprovider** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på **avsnittet REDIGERA INSTÄLLNINGAR** under avsnittet **SSO/SLO-inställningar.**

    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. I **popup-fönstret Inställningar** utför du följande steg:

    ![Konfiguration av Netskope-administratörskonsol](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Välj **Aktivera SSO**.

    b. I **textrutan FÖR IDP-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    c. I **textrutan IDP ENTITY ID** klistrar du in Azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    d. Öppna det nedladdade Base64-kodade certifikatet i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i **textrutan för IDP-certifikat.**

    e. Välj **Aktivera SSO**.

    f. I **textrutan URL för IDP SLO** klistrar du in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen.

    g. Klicka på **SKICKA**.

### <a name="create-netskope-administrator-console-test-user"></a>Skapa testanvändare för Netskope Administrator Console

1. Öppna en ny flik i webbläsaren och logga in på företagets webbplats för Netskope Administrator Console som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigeringsfönstret.

    ![Netskope-administratörskonsolens användarskapelse](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicka på fliken **Aktiv plattform.**

    ![Netskope-administratörskonsolens användarskapelse](./media/netskope-cloud-security-tutorial/user1.png)

1. Klicka på fliken **Användare.**

    ![Netskope-administratörskonsolens användarskapelse](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Netskope-administratörskonsolens användarskapelse](./media/netskope-cloud-security-tutorial/user-add.png)

1. Ange e-postadressen till den användare som du vill lägga till och klicka på **LÄGG TILL**.

    ![Netskope-administratörskonsolens användarskapelse](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Netskope Administrator Console på åtkomstpanelen bör du automatiskt loggas in på den Netskope-administratörskonsol som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Netskope Administrator Console med Azure AD](https://aad.portal.azure.com/)
