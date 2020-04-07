---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Catchpoint'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Självstudiekurs: Azure Active Directory enkel inloggningsintegrering med Catchpoint

I den här självstudien får du lära dig hur du integrerar Catchpoint med Azure Active Directory (Azure AD). När du integrerar Catchpoint med Azure AD kan du:

* Kontrollera användaråtkomst till Catchpoint från Azure AD.
* Aktivera automatisk Catchpoint-inloggning för användare med Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Catchpoint-prenumeration med enkel inloggning (SSO) aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Catchpoint stöder SP-initierad och IDP-initierad SSO.
* Catchpoint stöder jit-etablering (just-in-time).
* När du har konfigurerat Catchpoint kan du framtvinga sessionskontrollen. Den här försiktighetsåtgärden skyddar mot exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen är ett tillägg av villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Lägga till Catchpoint från galleriet

Om du vill konfigurera integreringen av Catchpoint i Azure AD lägger du till Catchpoint i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets-, skol- eller personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i den vänstra rutan.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Catchpoint** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Catchpoint** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurera och testa en azure AD-inloggning för Catchpoint

För att SSO ska fungera måste du länka en Azure AD-användare med en användare i Catchpoint. För den här självstudien konfigurerar vi en testanvändare som heter **B.Simon**. 

Fyll i följande avsnitt:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso)för att aktivera den här funktionen för dina användare.
    * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)för att testa Azure AD enkel inloggning med B.Simon.
    * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)för att aktivera B.Simon för att använda Azure AD enkel inloggning.
1. [Konfigurera Catchpoint SSO](#configure-catchpoint-sso)för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * [Skapa Catchpoint-testanvändare](#create-a-catchpoint-test-user)för att tillåta länkning av B.Simon Azure AD-testkontot till ett liknande användarkonto i Catchpoint.
1. [Testa SSO](#test-sso)för att kontrollera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg i Azure-portalen för att aktivera Azure AD SSO:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. På sidan **Catchpoint-programintegration** hittar du avsnittet **Hantera** och väljer enkel **inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för att redigera inställningarna för grundläggande **SAML-konfiguration.**

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Konfigurera det initierade läget för Catchpoint:
   - För **IDP-initierat**läge anger du värdena för följande fält:
     - För **identifierare:**`https://portal.catchpoint.com/SAML2`
     - För **svar URL:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - För **SP**SP-initierat läge väljer du **Ange ytterligare webbadresser** och anger följande värde:
     - För **sign-on URL:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint-programmet förväntar sig SAML-påståenden i ett visst format. Lägg till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande tabell innehåller en lista över standardattribut:

    | Namn | Källattribut|
    | ------------ | --------- |
    | Givennamn | användare.givenneame |
    | Efternamn | user.surname |
    | Emailaddress | user.mail |
    | Namn | user.userprincipalname |
    | Unik användaridentifierare | user.userprincipalname |

    ![Användarattribut & skadelista skärmdump](common/default-attributes.png)

1. Catchpoint-programmet förväntar sig också att ett annat attribut skickas i ett SAML-svar. Se följande tabell. Det här attributet är också ifyllt, men du kan granska och uppdatera det så att det passar dina behov.

    | Namn | Källattribut|
    | ------------ | --------- |
    | namnområde | user.assignedrole |

    > [!NOTE]
    > Anspråket `namespace` måste mappas med kontonamnet. Det här kontonamnet bör konfigureras med en roll i Azure AD som ska skickas tillbaka i SAML-svar. Mer information om roller i Azure AD finns i [Konfigurera rollanspråket som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Gå till sidan **Konfigurera enkel inloggning med SAML.** Leta reda på **certifikat (Base64)** i avsnittet **SAML-signeringscertifikat** . Välj **Hämta** om du vill spara certifikatet på datorn.

    ![Länken för hämtning av certifikat](common/certificatebase64.png)

1. Kopiera webbadresserna som du behöver i ett senare steg i avsnittet **Konfigurera Catchpoint.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet använder du Azure-portalen för att skapa en Azure AD-testanvändare som heter B.Simon.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** > .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Ange till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord.** Observera det visade lösenordsvärdet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Catchpoint.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **Catchpoint**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken "Lägg till användare"](common/add-assign-user.png)

1. Välj **B.Simon** i listan över användare **och grupper.** Klicka på **Markera** längst ned på skärmen.
1. Om du förväntar dig ett rollvärde i SAML-påståendet tittar du i dialogrutan **Välj roll** och väljer användarens roll i listan. Klicka på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-catchpoint-sso"></a>Konfigurera Catchpoint SSO

1. I ett annat webbläsarfönster loggar du in på Catchpoint-programmet som administratör.

1. Välj ikonen **Inställningar** och sedan **SSO Identity Provider**.

    ![Skärmbild av Catchpoint-inställningar med SSO Identity Provider markerad](./media/catchpoint-tutorial/configuration1.png)

1. På sidan **Enkel inloggning** anger du följande fält:

   ![Catchpoint Single Sign On sida skärmdump](./media/catchpoint-tutorial/configuration2.png)

   Field | Värde
   ----- | ----- 
   **Namespace** | Ett giltigt namnområdesvärde.
   **Utfärdare av identitetsprovider** | Värdet `Azure AD Identifier` från Azure-portalen.
   **Url för enkel inloggning** | Värdet `Login URL` från Azure-portalen.
   **Certifikat** | Innehållet i den `Certificate (Base64)` hämtade filen från Azure-portalen. Använd Anteckningar för att visa och kopiera.

   Du kan också ladda upp **FEDERATIONSMETADATA-XML:en** genom att välja alternativet **Ladda upp metadata.**

1. Välj **Spara**.

### <a name="create-a-catchpoint-test-user"></a>Skapa en Catchpoint-testanvändare

Catchpoint stöder just-in-time-användaretablering, vilket är aktiverat som standard. Du har inga åtgärdsobjekt i det här avsnittet. Om B.Simon inte redan finns som användare i Catchpoint skapas den efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du väljer Catchpoint-panelen i portalen Mina appar bör du automatiskt loggas in i Catchpoint-appen med SSO konfigurerat. Mer information om portalen Mina appar finns [i Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> När du är inloggad i Catchpoint-programmet via inloggningssidan anger du det giltiga **namnområdesvärdet** i fältet **Företagsautentiseringsuppgifter (SSO)** efter att ha angett **Catchpoint-autentiseringsuppgifter**och väljer **Logga in**.
> 
> ![Catchpoint-konfiguration](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Catchpoint med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
