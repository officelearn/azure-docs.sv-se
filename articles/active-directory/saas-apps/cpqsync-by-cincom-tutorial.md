---
title: 'Självstudiekurs: Azure Active Directory-integrering med CPQSync av Cincom | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CPQSync av Cincom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d9411095-859c-4223-8d0b-36d1f89b04d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc9cf236beab78d666f634489a205828050fae4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879564"
---
# <a name="tutorial-integrate-cpqsync-by-cincom-with-azure-active-directory"></a>Självstudiekurs: Integrera CPQSync av Cincom med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar CPQSync by Cincom med Azure Active Directory (Azure AD). När du integrerar CPQSync by Cincom med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CPQSync av Cincom.
* Gör att användarna automatiskt loggas in på CPQSync av Cincom med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* CpQSync by Cincom enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* CPQSync från Cincom stöder **SP och IDP** initierade SSO

## <a name="adding-cpqsync-by-cincom-from-the-gallery"></a>Lägga till CPQSync av Cincom från galleriet

Om du vill konfigurera integreringen av CPQSync by Cincom i Azure AD måste du lägga till CPQSync by Cincom från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **CPQSync av Cincom** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **CPQSync av Cincom** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cpqsync-by-cincom"></a>Konfigurera och testa en azure AD-inloggning för CPQSync by Cincom

Konfigurera och testa Azure AD SSO med CPQSync by Cincom med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i CPQSync av Cincom.

Så här konfigurerar och testar du Azure AD SSO med CPQSync by Cincom:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera CPQSync av Cincom SSO](#configure-cpqsync-by-cincom-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa CPQSync av Cincom testanvändare](#create-cpqsync-by-cincom-test-user)** - att ha en motsvarighet till B.Simon i CPQSync av Cincom som är kopplad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **CPQSync by Cincom-programintegration** och väljer **Enkel inloggning**. **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://cincom.oktapreview.com/sso/saml2/<CUSTOMURL>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://cincom.okta.com/sso/saml2/<CUSTOMDOMAIN>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://cincom.okta.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [CPQSync från Cincom Client support team](https://cpqsupport.cincomcpq.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera CPQSync by Cincom.**

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till CPQSync av Cincom.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **CPQSync av Cincom**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cpqsync-by-cincom-sso"></a>Konfigurera CPQSync av Cincom SSO

Om du vill konfigurera enkel inloggning på **CPQSync från Cincom-sidan** måste du skicka det nedladdade **certifikatet (Raw)** och lämpliga kopierade url:er från Azure-portalen till [CPQSync av Cincom supportteam](https://cpqsupport.cincomcpq.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cpqsync-by-cincom-test-user"></a>Skapa CPQSync av Cincom testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i CPQSync av Cincom. Arbeta med [CPQSync by Cincoms supportteam](https://cpqsupport.cincomcpq.com) för att lägga till användarna i CPQSync by Cincom-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på cpqsync by Cincom-panelen på åtkomstpanelen ska du automatiskt loggas in på cpqSync av Cincom som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

