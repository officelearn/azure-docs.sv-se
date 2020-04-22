---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Meraki Dashboard | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Meraki Dashboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 09766236-5de0-43fd-8950-5316390ce646
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571f4421a5d890fab31eda0125802d33918144ef
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726384"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Meraki Dashboard

I den här självstudien får du lära dig hur du integrerar Meraki Dashboard med Azure Active Directory (Azure AD). När du integrerar Meraki Dashboard med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Meraki Dashboard.
* Gör att användarna automatiskt loggas in på Meraki Dashboard med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En prenumeration på Meraki Dashboard enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Meraki Dashboard stöder **IDP** initierad SSO
* När du har konfigurerat Meraki Dashboard kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Lägga till Meraki Dashboard från galleriet

Om du vill konfigurera integreringen av Meraki Dashboard i Azure AD måste du lägga till Meraki Dashboard från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Meraki Dashboard i sökrutan i avsnittet **Lägg till från galleriet.** **Meraki Dashboard**
1. Välj **Meraki Dashboard** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-meraki-dashboard"></a>Konfigurera och testa en azure AD-inloggning för Meraki Dashboard

Konfigurera och testa Azure AD SSO med Meraki Dashboard med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Meraki Dashboard.

Om du vill konfigurera och testa Azure AD SSO med Meraki Dashboard slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Meraki Dashboard SSO](#configure-meraki-dashboard-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Meraki Dashboard testanvändare](#create-meraki-dashboard-test-user)** - att ha en motsvarighet till B.Simon i Meraki Dashboard som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **Meraki Dashboard**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**


1. Meraki Dashboard-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig Meraki Dashboard ansökan några fler attribut som ska skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn | Källattribut|
    | ---------------| --------- |
    | användarnamn | user.userprincipalname |
    | roll | user.assignedroles |

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Meraki-instrumentpanelen** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Meraki Dashboard.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Meraki Dashboard**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-meraki-dashboard-sso"></a>Konfigurera Meraki Dashboard SSO

1. I ett annat webbläsarfönster loggar du in på Meraki-instrumentpanelen som administratör.

1. Navigera till -> **Organisationsinställningar**. **Organization**

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/configure1.png)

1. Under Autentisering ändrar du **SAML SSO** till **SAML SSO aktiverat**.

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/configure2.png)

1. Klicka på **Lägg till ett SAML-IDP**.

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/configure3.png)

1. Klistra in **tumavtrycksvärdet,** som du har kopierat från Azure-portalen i **X.590 cert SHA1** fingeravtryckstextruta. Klicka sedan på **Spara**.

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Skapa Meraki Dashboard-testanvändare

1. I ett annat webbläsarfönster loggar du in på Meraki-instrumentpanelen som administratör.

1. Navigera till -> **organisationsadministratörer**. **Organization**

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/user1.png)

1. Klicka på knappen Lägg till **SAML-roll** i avsnittet SAML-administratörsroller.

    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/user2.png)

1. Ange roll **meraki_full_admin**, markera **organisationsåtkomst** som **fullständig** och klicka på **Skapa roll**. Upprepa processen för **meraki_readonly_admin**, den här gången markerar **organisationens åtkomst** som **skrivskyddad** ruta.
 
    ![Konfiguration av Meraki-instrumentpanelen](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Meraki Dashboard på åtkomstpanelen bör du automatiskt loggas in på merakiinstrumentpanelen som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Meraki Dashboard med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Meraki Dashboard med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

