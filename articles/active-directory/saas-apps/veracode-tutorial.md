---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Veracode | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73043563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (Azure AD). När du integrerar Veracode med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Veracode.
* Gör att användarna automatiskt loggas in på Veracode med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Veracode-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Veracode stöder identitetsprovider initierad SSO och just-in-time-användaretablering.

## <a name="add-veracode-from-the-gallery"></a>Lägg till Veracode från galleriet

Om du vill konfigurera integreringen av Veracode i Azure AD lägger du till Veracode från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv "Veracode" i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Veracode** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Konfigurera och testa en azure AD-inloggning för Veracode

Konfigurera och testa Azure AD SSO med Veracode med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i Veracode.

Om du vill konfigurera och testa Azure AD SSO med Veracode slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera Veracode SSO](#configure-veracode-sso)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa en Veracode-testanvändare](#create-veracode-test-user)** för att ha en motsvarighet till B.Simon i Veracode kopplad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Veracode-programintegration på **Azure-portalen.** [Azure portal](https://portal.azure.com/) Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML, med pennikonen markerad](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Välj **Spara**.

1. Sök upp **certifikat (Base64)** i avsnittet **SAML Signering Certificate** på sidan Konfigurera enkel inloggning med **SAML.** Välj **Hämta** om du vill hämta certifikatet och spara det på datorn.

    ![Skärmbild av avsnittet SAML-signeringscertifikat, med länken Hämta markerad](common/certificatebase64.png)

1. Veracode förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Skärmbild av avsnittet Användarattribut & anspråk](common/default-attributes.png)

1. Veracode förväntar sig också några fler attribut som ska skickas tillbaka i SAML svar. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | förnamn |User.givenname |
    | efternamn |Användare.efternamn |
    | e-post |User.mail |

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Veracode** baserat på dina krav.

    ![Skärmbild av Avsnittet Konfigurera Veracode, med konfigurationsadresser markerade](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Konfigurera Veracode SSO

1. Logga in på veracode-företagets webbplats som administratör i ett annat webbläsarfönster.

1. Välj > **Inställningaradministratör**högst upp **Settings**.
   
    ![Skärmbild av Veracode Administration, med ikonen Inställningar och Administratör markerad](./media/veracode-tutorial/ic802911.png "Administration")

1. Välj fliken **SAML.**

1. Gör följande i avsnittet **Organisations-SAML-inställningar:**

    ![Skärmbild av avsnittet Saml-inställningar för Organisation](./media/veracode-tutorial/ic802912.png "Administration")

    a.  För **Utfärdare**klistrar du in värdet för **Azure AD-identifieraren** som du har kopierat från Azure-portalen.

    b. För **certifikat för kontrollsignering**väljer du **Välj fil** för att ladda upp det nedladdade certifikatet från Azure-portalen.

    c. För **självregistrering**väljer du **Aktivera självregistrering**.

1. I avsnittet **Självregistreringsinställningar** utför du följande steg och väljer sedan **Spara:**

    ![Skärmbild av avsnittet Inställningar för självregistrering, med olika alternativ markerade](./media/veracode-tutorial/ic802913.png "Administration")

    a. För **Ny användaraktivering**väljer du **Ingen aktivering krävs**.

    b. För **uppdateringar av användardata**väljer du **Inställningar Veracode Användardata**.

    c. För **SAML-attributinformation**väljer du följande:
      * **Användarroller**
      * **Principadministratör**
      * **Granskare**
      * **Säkerhetsledning**
      * **Chef**
      * **Inlämnare**
      * **Creator**
      * **Alla skanningstyper**
      * **Team medlemskap**
      * **Standardteam**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** >.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**

   1. För **Namn** `B.Simon`anger du .  
   1. För **Användarnamn**anger username@companydomain.extensiondu . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösenord**och skriv sedan ned värdet som visas.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Veracode.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **Veracode**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon**från **Användare**och grupper i dialogrutan **Användare och grupper** . Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-veracode-test-user"></a>Skapa Veracode-testanvändare

För att logga in på Veracode måste Azure AD-användare etableras i Veracode. Den här uppgiften är automatiserad och du behöver inte göra något manuellt. Användare skapas automatiskt om det behövs under det första enkla inloggningsförsöket.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa veracode för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer **Veracode** på åtkomstpanelen ska du automatiskt loggas in på den Veracode som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Veracode med Azure AD](https://aad.portal.azure.com/)