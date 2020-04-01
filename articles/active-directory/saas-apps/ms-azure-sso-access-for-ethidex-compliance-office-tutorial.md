---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med MS Azure SSO Access för Ethidex Compliance Office™ | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MS Azure SSO Access för Ethidex Compliance Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70844363"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med MS Azure SSO Access för Ethidex Compliance Office™

I den här självstudien får du lära dig hur du integrerar MS Azure SSO Access för Ethidex Compliance Office™ med Azure Active Directory (Azure AD). När du integrerar MS Azure SSO Access för Ethidex Compliance Office™ med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MS Azure SSO Access för Ethidex Compliance Office™.
* Gör att användarna automatiskt loggas in på MS Azure SSO Access för Ethidex Compliance Office™ med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* MS Azure SSO Access för Ethidex Compliance Office™ SSO -aktiverad prenumeration (Single Sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* MS Azure SSO Access för Ethidex Compliance Office™ stöder **IDP-initierad** SSO

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Lägga till MS Azure SSO Access för Ethidex Compliance Office™ från galleriet

Om du vill konfigurera integreringen av MS Azure SSO Access för Ethidex Compliance Office™ i Azure AD måste du lägga till MS Azure SSO Access för Ethidex Compliance Office™ från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **MS Azure SSO Access för Ethidex Compliance Office i** avsnittet Lägg till från **galleriet™** i sökrutan.
1. Välj **MS Azure SSO Access för Ethidex Compliance Office™** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Konfigurera och testa en enkel Azure AD-inloggning för MS Azure SSO Access för Ethidex Compliance Office™

Konfigurera och testa Azure AD SSO med MS Azure SSO Access för Ethidex Compliance Office™ med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i MS Azure SSO Access för Ethidex Compliance Office™.

Om du vill konfigurera och testa Azure AD SSO med MS Azure SSO Access för Ethidex Compliance Office™ slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera MS Azure SSO Access för Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa MS Azure SSO Access för Ethidex Compliance Office-testanvändare](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** – om du vill ha en motsvarighet till B.Simon i MS Azure SSO Access för Ethidex Compliance Office™ som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på **sidan MS Azure SSO Access för Ethidex Compliance Office™** programintegrering och väljer enkel **inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`com.ethidex.prod.<CLIENTID>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [MS Azure SSO Access för Ethidex Compliance Office™ supportteam](mailto:support@ethidex.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. MS Azure SSO Access för Ethidex Compliance Office™ programprogram förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar till din SAML-tokenattributkonfiguration. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. MS Azure SSO Access för Ethidex Compliance Office™ programmet förväntar **namnidentifierare** som ska mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera** ikonen och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera MS Azure SSO Access för Ethidex Compliance Office™** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MS Azure SSO Access för Ethidex Compliance Office™.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **MS Azure SSO Access för Ethidex Compliance Office™**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Konfigurera MS Azure SSO Access för Ethidex Compliance Office SSO

Om du vill konfigurera enkel inloggning på **MS Azure SSO Access för Ethidex Compliance Office™** sidan måste du skicka det nedladdade **certifikatet (Raw)** och lämpliga kopierade url:er från Azure-portalen till [MS Azure SSO Access för Ethidex Compliance Office™ supportteam](mailto:support@ethidex.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Skapa MS Azure SSO Access för Ethidex Compliance Office-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i MS Azure SSO Access för Ethidex Compliance Office™. Arbeta med [MS Azure SSO Access för Ethidex Compliance Office™ supportteam](mailto:support@ethidex.com) för att lägga till användarna i MS Azure SSO Access för Ethidex Compliance Office™-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen MS Azure SSO Access för Ethidex Compliance Office™ på åtkomstpanelen, ska du automatiskt loggas in på MS Azure SSO Access för Ethidex Compliance Office™ som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova MS Azure SSO Access för Ethidex Compliance Office™ med Azure AD](https://aad.portal.azure.com/)

