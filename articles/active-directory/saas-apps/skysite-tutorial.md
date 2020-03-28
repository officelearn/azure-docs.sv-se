---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SKYSITE | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SKYSITE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 04c6a47a-1730-4acf-bc5c-a04daccff9b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ef7f4201e9613cc6fa4391bc28d257272fa1c7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SKYSITE

I den här självstudien får du lära dig hur du integrerar SKYSITE med Azure Active Directory (Azure AD). När du integrerar SKYSITE med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SKYSITE.
* Gör att användarna automatiskt loggas in på SKYSITE med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Skysite enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SKYSITE stöder **IDP-initierad** SSO

* SKYSITE stöder just in time-användaretablering **Just In Time**

## <a name="adding-skysite-from-the-gallery"></a>Lägga till SKYSITE från galleriet

Om du vill konfigurera integreringen av SKYSITE i Azure AD måste du lägga till SKYSITE från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SKYSITE** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SKYSITE** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Konfigurera och testa en azure AD-inloggning för SKYSITE

Konfigurera och testa Azure AD SSO med SKYSITE med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SKYSITE.

Så här konfigurerar och testar du Azure AD SSO med SKYSITE:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SKYSITE SSO](#configure-skysite-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa SKYSITE-testanvändare](#create-skysite-test-user)** – om du vill ha en motsvarighet till B.Simon i SKYSITE som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På sidan **SkysITE-programintegrering** i [Azure-portalen](https://portal.azure.com/)klickar du på **fliken Egenskaper** och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/skysite-tutorial/config05.png)

    * Kopiera **användaråtkomst-URL:en** och du måste klistra in den i **avsnittet Konfigurera SKYSITE SSO**, vilket förklaras senare i självstudien.

1. På sidan För integrering av **SKYSITE-program** navigerar du till **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. SKYSITE-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig SKYSITE-programmet att få fler attribut skickas tillbaka i SAML-svar. Gör följande i avsnittet **Användarattribut & anspråk** i dialogrutan **Gruppanspråk (förhandsversion):**

    a. Klicka på **pennan** **bredvid Grupper som returneras i anspråk**.

    ![image](./media/skysite-tutorial/config01.png)

    ![image](./media/skysite-tutorial/config02.png)

    b. Välj **Alla grupper** i radiolistan.

    c. Välj **källattribut för** **grupp-ID**.

    d. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera SKYSITE.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SKYSITE.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SKYSITE**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="configure-skysite-sso"></a>Konfigurera SKYSITE SSO

1. Öppna ett nytt webbläsarfönster och logga in på skysite-företagets webbplats som administratör och utför följande steg:

4. Klicka på **Inställningar** längst upp till höger på sidan och navigera sedan till **kontoinställning**.

    ![Konfiguration](./media/skysite-tutorial/config03.png)

5. Växla till fliken **Enkel inloggning (SSO)** gör du följande:

    ![Konfiguration](./media/skysite-tutorial/config04.png)

    a. Klistra in värdet för **användaråtkomst-URL**i textrutan **Identitetsprovider** logga in , som du har kopierat från **egenskapsfliken** i Azure-portalen.

    b. Klicka på **Ladda upp certifikat**om du vill ladda upp det Base64-kodade certifikat som du har hämtat från Azure-portalen.

    c. Klicka på **Spara**.

### <a name="create-skysite-test-user"></a>Skapa SKYSITE-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i SKYSITE. SKYSITE stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i SKYSITE skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SKYSITE-panelen på åtkomstpanelen ska du automatiskt loggas in på den SKYSITE som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SKYSITE med Azure AD](https://aad.portal.azure.com/)

