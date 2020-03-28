---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Helpshift | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Helpshift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 114de95d-e9a7-4f87-b14d-54b91a63ce49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f7e932a3b71e802c5b814f6dfb59922148c2519
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75533876"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Helpshift

I den här självstudien får du lära dig hur du integrerar Helpshift med Azure Active Directory (Azure AD). När du integrerar Helpshift med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Helpshift.
* Gör att användarna automatiskt loggas in på Helpshift med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Helpshift stöder **SP och IDP** initierad SSO

## <a name="adding-helpshift-from-the-gallery"></a>Lägga till Helpshift från galleriet

Om du vill konfigurera integreringen av Helpshift i Azure AD måste du lägga till Helpshift från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Helpshift** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Helpshift** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Konfigurera och testa en azure AD-inloggning för Helpshift

Konfigurera och testa Azure AD SSO med Helpshift med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Helpshift.

Om du vill konfigurera och testa Azure AD SSO med Helpshift slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Helpshift SSO](#configure-helpshift-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Helpshift-testanvändare](#create-helpshift-test-user)** – om du vill ha en motsvarighet till B.Simon i Helpshift som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Helpshift** **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<YourDOMAIN>.helpshift.com/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    d. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren, svars-URL:en, inloggnings-URL och Relay State. Kontakta [Helpshift Client supportteam](mailto:support@helpshift.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera helpshift** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Helpshift.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Helpshift**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-helpshift-sso"></a>Konfigurera HjälpskiftSSO

1. I en annan webbläsare loggar du in på ditt Helpshift-program som administratör.

1. Öppna **instrumentpanelen** för Helpshift och klicka på **ikonen Inställningar**.

    ![Den hjälpskiftade konfigurationen](./media/helpshift-tutorial/configuration01.png)

1. Klicka på fliken **Integreringar** och utför följande steg:

    ![Den hjälpskiftade konfigurationen](./media/helpshift-tutorial/configuration02.png)

    a. Aktivera **enkel inloggning(SAML – SSO).**

    b. Välj **identitetsprovider(IDP)** som **Azure Active Directory**.

    c. I **sms:et för URL-url för SAML 2.0** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    d. Öppna nedladdad **certifikatfil (Base64)** i Anteckningar, kopiera innehållet i filen (utan att använda textrutan "–BEGIN CERTIFICATE–" och "–END CERTIFICATE – –" och klistra in den i textrutan **X.509 Certifikat.**

    e. I textrutan **Utfärdare-URL** klistrar du in azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    f. Klicka på **TILLÄMPA ÄNDRINGAR**.

### <a name="create-helpshift-test-user"></a>Skapa helpshift-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Helpshift. Arbeta med [Helpshift Client supportteam](mailto:support@helpshift.com) för att lägga till användarna i Helpshift-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Hjälpskiftning på åtkomstpanelen bör du automatiskt loggas in på den hjälpväxling som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Helpshift med Azure AD](https://aad.portal.azure.com/)