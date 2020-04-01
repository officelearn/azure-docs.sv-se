---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SD Elements | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SD-element.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081684"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SD-element

I den här självstudien får du lära dig hur du integrerar SD-element med Azure Active Directory (Azure AD). När du integrerar SD-element med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SD-element.
* Gör att användarna automatiskt loggas in på SD-element med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SD Elements enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SD Elements stöder **IDP** initierad SSO

## <a name="adding-sd-elements-from-the-gallery"></a>Lägga till SD-element från galleriet

Om du vill konfigurera integreringen av SD-element i Azure AD måste du lägga till SD-element från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SD-element** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SD-element** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Konfigurera och testa en azure AD-inloggning för SD-element

Konfigurera och testa Azure AD SSO med SD-element med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SD-element.

Så här konfigurerar och testar du Azure AD SSO med SD-element:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SD Elements SSO](#configure-sd-elements-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa SD Elements-testanvändare](#create-sd-elements-test-user)** – om du vill ha en motsvarighet till B.Simon i SD-element som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan Integrering av **SD** **Elements-programmet**hittar du avsnittet **Hantera** och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [SD Elements Client support team](mailto:support@sdelements.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SD Elements-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig SD Elements ansökan få fler attribut som ska skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | --- | --- |
    | e-post |user.mail |
    | förnamn |user.givenname |
    | efternamn |user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera SD-element** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SD-element.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SD-element**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sd-elements-sso"></a>Konfigurera SD-element SSO

1. Om du vill aktivera enkel inloggning kontaktar du [SD Elements supportteam](mailto:support@sdelements.com) och förser dem med den nedladdade certifikatfilen.

1. I ett annat webbläsarfönster loggar du in på din SD Elements-klient som administratör.

1. Klicka på **System**högst upp och sedan **Enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Gör följande i dialogrutan **Inställningar för enkel inloggning:**

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Som **SSO-typ**väljer du **SAML**.

    b. Klistra in värdet för **Azure AD-identifierare**i textrutan **identitetsproviderns entitets-ID** som du har kopierat från Azure-portalen.

    c. Klistra in värdet för **inloggnings-URL**i textrutan för en enskild inloggningstjänst i textrutan **För identitetsprovider med enkel inloggningstjänst** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-sd-elements-test-user"></a>Skapa SD Elements-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i SD Elements. När det gäller SD-element är det en manuell uppgift att skapa SD Elements-användare.

**Så här skapar du B.Simon i SD Elements:**

1. I ett webbläsarfönster loggar du in på företagets webbplats för SD Elements som administratör.

1. Klicka på **Användarhantering**på menyn högst upp och sedan **Användare**.

    ![Skapa en SD Elements-testanvändare](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klicka på **Lägg till ny användare**.

    ![Skapa en SD Elements-testanvändare](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Gör följande i dialogrutan **Lägg till ny användare:**

    ![Skapa en SD Elements-testanvändare](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. I **textrutan E-post** anger du e-postmeddelandet för användaren som **b.simon@contoso.com**.

    b. Ange **First Name** förnamnet för användaren som **B.**.

    c. Ange efternamn för användaren som **Simon**i textrutan **Efternamn** .

    d. Som **roll**väljer du **Användare**.

    e. Klicka på **Skapa användare**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SD-element på åtkomstpanelen bör du automatiskt loggas in på de SD-element som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SD-element med Azure AD](https://aad.portal.azure.com/)