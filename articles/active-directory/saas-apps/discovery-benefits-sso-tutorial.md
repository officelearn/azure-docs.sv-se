---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Discovery Benefits SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Discovery Benefits SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266145"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Discovery Benefits SSO

I den här självstudien får du lära dig hur du integrerar Identifieringsfördelar SSO med Azure Active Directory (Azure AD). När du integrerar Identifieringsförmåner SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Discovery Benefits SSO.
* Gör det möjligt för användarna att automatiskt loggas in på Discovery Benefits SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Identifieringsförmåner SSO enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Discovery Benefits SSO stöder **IDP-initierad** SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Lägga till Discovery Benefits SSO från galleriet

Om du vill konfigurera integreringen av SSO för identifieringsförmåner i Azure AD måste du lägga till SSO för identifieringsförmåner från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv SSO för **identifieringsförmåner** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SSO för identifieringsförmåner** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Konfigurera och testa Azure AD enkel inloggning för identifieringsförmåner SSO

Konfigurera och testa Azure AD SSO med Discovery Benefits SSO med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SSO för identifieringsförmåner.

Så här konfigurerar och testar du Azure AD SSO med SSO för identifieringsförmåner:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera identifieringsförmåner SSO SSO](#configure-discovery-benefits-sso-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa identifieringsfördelar SSO-testanvändare](#create-discovery-benefits-sso-test-user)** – om du vill ha en motsvarighet till B.Simon i Identifieringsförmåner SSO som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **Identifieringsförmåner** för SSO-programintegrering och väljer **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.** 

1. Discovery Benefits SSO-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

    a. Klicka på **ikonen Redigera** för att öppna dialogrutan **Unik användaridentifierare (namn-ID).**

    ![SSO-konfiguration för identifieringsfördelar](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![SSO-konfiguration för identifieringsfördelar](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Klicka på **ikonen Redigera** för att öppna dialogrutan **Hantera omvandling.**

    c. Skriv den **ToUppercase()** som visas för den raden i textrutan **Omformning.**

    d. Skriv **Parameter 1** parametern som `<Name Identifier value>`.

    e. Klicka på **Lägg till**.

    > [!NOTE]
    > Identifieringsförmåner SSO kräver att ett fast strängvärde skickas i fältet **Unik användaridentifierare (name ID)** för att få integreringen att fungera. Azure AD stöder för närvarande inte den här funktionen, så som en lösning kan du använda **ToUpper-** eller **ToLower-omvandlingar** av NameID för att ange ett fast strängvärde som visas ovan i skärmbilden.

    f. Vi har automatiskt fyllt i de ytterligare anspråk som`SSOInstance` `SSOID`krävs för SSO-konfiguration ( och ). Använd ikonen **Redigera** för att mappa värdena enligt din organisation.

    ![SSO-konfiguration för identifieringsfördelar](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera identifieringsförmåner.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Discovery Benefits SSO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj SSO för **identifieringsförmåner**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-discovery-benefits-sso-sso"></a>Konfigurera identifieringsfördelar SSO SSO

Om du vill konfigurera enkel inloggning på **SSO-sidan för identifieringsförmåner** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [SSO-supportteamet för identifieringsförmåner](mailto:Jsimpson@DiscoveryBenefits.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-discovery-benefits-sso-test-user"></a>Skapa SSO-testanvändare för identifieringsförmåner

I det här avsnittet skapar du en användare som heter Britta Simon i Discovery Benefits SSO. Arbeta med [SSO-supportteamet för identifieringsförmåner](mailto:Jsimpson@DiscoveryBenefits.com) för att lägga till användarna i SSO-plattformen Discovery Benefits. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Identifieringsförmåner på åtkomstpanelen bör du automatiskt loggas in på SSO för identifieringsförmåner som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova identifieringsförmåner SSO med Azure AD](https://aad.portal.azure.com/)

