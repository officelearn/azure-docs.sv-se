---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Yuhu Property Management Platform | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yuhu Property Management Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5f7846fe-49f8-48b4-abda-a3719cb18c15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f15dfa36c24c7a84e254b110e9cceae54c8786
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75533246"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Yuhu Property Management Platform

I den här självstudien får du lära dig hur du integrerar Yuhu Property Management Platform med Azure Active Directory (Azure AD). När du integrerar Yuhu Property Management Platform med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Yuhu Property Management Platform.
* Gör att användarna automatiskt loggas in på Yuhu Property Management Platform med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Yuhu Property Management Platform enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Yuhu Property Management Platform stöder **SP** initierade SSO

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Lägga Yuhu Property Management Platform från galleriet

Om du vill konfigurera integreringen av Yuhu Property Management Platform i Azure AD måste du lägga till Yuhu Property Management Platform från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Yuhu Property Management Platform** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Yuhu Property Management Platform** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Konfigurera och testa en enda Azure AD-inloggning för Yuhu Property Management Platform

Konfigurera och testa Azure AD SSO med Yuhu Property Management Platform med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Yuhu Property Management Platform.

Om du vill konfigurera och testa Azure AD SSO med Yuhu Property Management Platform slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Yuhu Property Management Platform SSO](#configure-yuhu-property-management-platform-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Testanvändare](#create-yuhu-property-management-platform-test-user)** för Yuhu Property Management Platform – om du vill ha en motsvarighet till B.Simon i Yuhu Property Management Platform som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på programintegreringssidan för **Yuhu Property Management Platform** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.yuhu.io/companies`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `yuhu-<ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Yuhu Property Management Platform Client supportteam](mailto:hello@yuhu.io) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Yuhu Property Management Platform-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar Yuhu Property Management Platform ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname ||
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser på avsnittet **Konfigurera Yuhu Property Management Platform** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yuhu Property Management Platform.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Yuhu Property Management Platform**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-yuhu-property-management-platform-sso"></a>Konfigurera Yuhu Property Management Platform SSO

Om du vill konfigurera enkel inloggning på **Yuhu Property Management Platform-sidan** måste du skicka det nedladdade **certifikatet (Raw)** och lämpliga kopierade url:er från Azure-portalen till [Yuhu Property Management Platform supportteam](mailto:hello@yuhu.io). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-yuhu-property-management-platform-test-user"></a>Skapa Testanvändare för Testanvändare för Yuhu Property Management Platform

I det här avsnittet skapar du en användare som heter B.Simon i Yuhu Property Management Platform. Arbeta med [Yuhu Property Management Platform supportteam](mailto:hello@yuhu.io) för att lägga till användarna i Yuhu Property Management Platform plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Yuhu Property Management Platform i åtkomstpanelen bör du automatiskt loggas in på Yuhu Property Management Platform som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Yuhu Property Management Platform med Azure AD](https://aad.portal.azure.com/)