---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Catchpoint | Microsoft-dokument'
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
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Catchpoint

I den här självstudien får du lära dig hur du integrerar Catchpoint med Azure Active Directory (Azure AD). När du integrerar Catchpoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Catchpoint.
* Gör att användarna automatiskt loggas in på Catchpoint med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Catchpoint enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Catchpoint stöder **SP och IDP** initierad SSO
* Catchpoint stöder just in time-användaretablering **Just In Time**
* När du har konfigurerat Catchpoint kan du framtvinga sessionskontroll, vilket skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Lägga till Catchpoint från galleriet

Om du vill konfigurera integreringen av Catchpoint i Azure AD måste du lägga till Catchpoint från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Catchpoint** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Catchpoint** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurera och testa en azure AD-inloggning för Catchpoint

Konfigurera och testa Azure AD SSO med Catchpoint med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Catchpoint.

Om du vill konfigurera och testa Azure AD SSO med Catchpoint slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Catchpoint SSO](#configure-catchpoint-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Catchpoint-testanvändare](#create-catchpoint-test-user)** – om du vill ha en motsvarighet till B.Simon i Catchpoint som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge anger du värdena för följande fält:

    a. Skriv URL:en i textrutan **Identifierare:**`https://portal.catchpoint.com/SAML2`

    b. Skriv URL:en i textrutan **Svara** URL:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar catchpoint-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------ | --------- |
    | namnområde | user.assignedrole |

    > [!NOTE]
    > namnområdesanspråk måste mappas med kontonamnet. Det här kontonamnet bör ställas in som rollerna i Azure AD som skickas tillbaka i SAML-svar. Se den här [artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) om du vill lära dig hur du ställer in rollerna

1. På sidan **Installation enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Catchpoint** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Catchpoint.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Catchpoint**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-catchpoint-sso"></a>Konfigurera Catchpoint SSO

1. I ett annat webbläsarfönster loggar du in på Catchpoint-programmet som administratör.

1. Klicka på ikonen **Inställningar** och välj **SSO Identity Provider**.

    ![Catchpoint-konfiguration](./media/catchpoint-tutorial/configuration1.png)

1. Gör följande på sidan **Enkel inloggning:**

    ![Catchpoint-konfiguration](./media/catchpoint-tutorial/configuration2.png)

    1. Ange ett giltigt namnområdesvärde i textrutan **Namnområde.**

    1. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) anger du värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    1. I textrutan **Enkel inloggningsadress** anger du värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    1. Öppna den nedladdade **certifikatfilen (Base64)** i Anteckningar, kopiera innehållet i certifikatfilen och klistra in den i **certifikattextrutan.**

    1. Du kan också ladda upp **FEDERATIONSMETADATA-XML:en** genom att klicka på alternativet **Ladda upp metadata.**

    1. Klicka på **Spara**.

### <a name="create-catchpoint-test-user"></a>Skapa Catchpoint-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Catchpoint. Catchpoint stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Catchpoint skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Catchpoint-panelen på åtkomstpanelen ska du automatiskt loggas in på den Catchpoint som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> När du loggar in på Catchpoint-programmet via inloggningssidan anger du det giltiga **namnområdesvärdet** i textrutan **Företagsautentiseringsuppgifter (SSO)** efter att ha angett **Catchpoint-autentiseringsuppgifter**och klickar på **Logga in**.

![Catchpoint-konfiguration](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Catchpoint med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)