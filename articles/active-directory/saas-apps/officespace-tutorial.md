---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med OfficeSpace Software | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OfficeSpace Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561722"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med OfficeSpace-programvara

I den här självstudien får du lära dig hur du integrerar OfficeSpace-programvara med Azure Active Directory (Azure AD). När du integrerar OfficeSpace-programvara med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OfficeSpace-programvara.
* Gör att användarna automatiskt loggas in på OfficeSpace-programvaran med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* OfficeSpace Software enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* OfficeSpace Software stöder **SP** initierade SSO


* OfficeSpace-programvara stöder just **in time-användares** etablering


## <a name="adding-officespace-software-from-the-gallery"></a>Lägga till OfficeSpace-programvara från galleriet

Om du vill konfigurera integreringen av OfficeSpace-programvara i Azure AD måste du lägga till OfficeSpace-programvara från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **OfficeSpace Software** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **OfficeSpace-programvara** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Konfigurera och testa en enda Azure AD-inloggning för OfficeSpace-programvara

Konfigurera och testa Azure AD SSO med OfficeSpace-programvara med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i OfficeSpace-programvaran.

Så här konfigurerar och testar du Azure AD SSO med OfficeSpace-programvara:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera OfficeSpace Software SSO](#configure-officespace-software-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa OfficeSpace Software-testanvändare](#create-officespace-software-test-user)** – om du vill ha en motsvarighet till B.Simon i OfficeSpace-programvaran som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **OfficeSpace Software** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta Supportteamet för [OfficeSpace Software Client](mailto:support@officespacesoftware.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. OfficeSpace-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. OfficeSpace Software-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. Förutom ovan förväntar sig OfficeSpace Software-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | e-post | user.mail |
    | namn | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera OfficeSpace-program** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OfficeSpace-programvara.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **OfficeSpace Software**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="configure-officespace-software-sso"></a>Konfigurera SSO för OfficeSpace-programvara

1. Logga in på OfficeSpace Software-klienten som administratör i ett annat webbläsarfönster.

2. Gå till **Inställningar** och klicka på **Kopplingar**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klicka på **SAML-autentisering**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Gör följande i avsnittet **SAML-autentisering**:

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Klistra in värdet för **url-URL för utloggning** som du har kopierat från Azure-portalen i textrutan **Logout-provider** url.

    b. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **klient-idp-måladress.**

    c. Klistra in **tumavtrycksvärdet** som du har kopierat från Azure-portalen i textrutan för fingeravtryck i **klient-IP-certifikatet.** 

    d. Klicka på **Spara inställningar**.

### <a name="create-officespace-software-test-user"></a>Skapa officespace-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i OfficeSpace Software. OfficeSpace Software stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OfficeSpace-programvaran skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta Supportteamet för [OfficeSpace-programvaran](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen OfficeSpace-programvara på åtkomstpanelen bör du automatiskt loggas in på den OfficeSpace-programvara som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova OfficeSpace-programvara med Azure AD](https://aad.portal.azure.com/)

