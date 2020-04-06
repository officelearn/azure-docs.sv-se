---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Zscaler B2B User Portal | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler B2B User Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75b90449-095d-4e47-a1fd-8b5cbbfe7e3d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28af94c92fa56d5217aee91053521d2c12b0aef
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Zscaler B2B-användarportal

I den här självstudien får du lära dig hur du integrerar Zscaler B2B User Portal med Azure Active Directory (Azure AD). När du integrerar Zscaler B2B User Portal med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler B2B User Portal.
* Aktivera dina användare automatiskt inloggade på Zscaler B2B User Portal med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Zscaler B2B User Portal enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Zscaler B2B User Portal stöder **IDP** initierad SSO

* Zscaler B2B User Portal stöder just in time-användaretablering **Just In Time**

* När du har konfigurerat Zscaler B2B User Portal kan du framtvinga Sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Lägga till Zscaler B2B User Portal från galleriet

Om du vill konfigurera integreringen av Zscaler B2B User Portal i Azure AD måste du lägga till Zscaler B2B User Portal från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Zscaler B2B User Portal** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Zscaler B2B User Portal** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Konfigurera och testa Azure AD enkel inloggning för Zscaler B2B User Portal

Konfigurera och testa Azure AD SSO med Zscaler B2B User Portal med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler B2B User Portal.

Så här konfigurerar och testar du Azure AD SSO med Zscaler B2B User Portal:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Zscaler B2B User Portal SSO](#configure-zscaler-b2b-user-portal-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Zscaler B2B User Portal testanvändare](#create-zscaler-b2b-user-portal-test-user)** - att ha en motsvarighet till B.Simon i Zscaler B2B User Portal som är länkad till Azure AD representation av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Zscaler B2B User** Portal-programintegrering och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Zscaler B2B User Portal Client supportteam](https://help.zscaler.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Zscaler B2B-användarportal.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler B2B User Portal.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Zscaler B2B User Portal**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Konfigurera Zscaler B2B User Portal SSO

1. Öppna ett nytt webbläsarfönster och logga in på zscaler B2B User Portal-företagets webbplats som administratör och utför följande steg:

1. Klicka på **Administration** till vänster på menyn och navigera till **avsnittet AUTENTISERING** på **IdP-konfiguration**.

    ![Zscaler administratör för privat åtkomst administratör](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Klicka på **Lägg till IdP-konfiguration**längst upp till höger . 

    ![Zscaler Private Access-administratörs-IDP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Gör följande på sidan **Lägg till IdP-konfiguration:**
 
    ![Zscaler Private Access-administratör välj](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klicka på **Välj fil** om du vill överföra den nedladdade metadatafilen från Azure AD i fältet Ladda **upp IdP-metadatafil.**

    b. Den läser **IdP-metadata** från Azure AD och fyller i all fältinformation som visas nedan.

    ![Zscaler Privat åtkomstadministratör config](./media/zscaler-b2b-user-tutorial/config.png)

    c. Välj din domän från fältet **Domäner.**
    
    d. Klicka på **Spara**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Skapa Zscaler B2B User Portal testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Zscaler B2B User Portal. Zscaler B2B User Portal stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Zscaler B2B User Portal skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på zscaler B2B User Portal-panelen på åtkomstpanelen ska du automatiskt loggas in på Zscaler B2B-användarportalen som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Zscaler B2B-användarportal med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)