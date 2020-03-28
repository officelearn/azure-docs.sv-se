---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Hosted MyCirqa SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hosted MyCirqa SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4337e343-180d-475d-83ac-6f5b5f133dfe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc6cd36190ca3847fdc9e3f6935aed5c78e0942
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75662341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Hosted MyCirqa SSO

I den här självstudien får du lära dig hur du integrerar Hosted MyCirqa SSO med Azure Active Directory (Azure AD). När du integrerar Hosted MyCirqa SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Hosted MyCirqa SSO.
* Gör att användarna automatiskt loggas in på Hosted MyCirqa SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Värd MyCirqa SSO enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Hosted MyCirqa SSO stöder **SP** initierade SSO

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Lägga till Hosted MyCirqa SSO från galleriet

Om du vill konfigurera integreringen av Hosted MyCirqa SSO i Azure AD måste du lägga till Hosted MyCirqa SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Hosted MyCirqa SSO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Hosted MyCirqa SSO** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Konfigurera och testa en azure AD-enkel inloggning för värdbaserad MyCirqa SSO

Konfigurera och testa Azure AD SSO med Hosted MyCirqa SSO med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Hosted MyCirqa SSO.

Så här konfigurerar och testar du Azure AD SSO med Hosted MyCirqa SSO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Hosted MyCirqa SSO](#configure-hosted-mycirqa-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa hosted MyCirqa SSO-testanvändare](#create-hosted-mycirqa-sso-test-user)** - om du vill ha en motsvarighet till B.Simon i Hosted MyCirqa SSO som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Hosted MyCirqa SSO-programintegration** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Hosted MyCirqa SSO Client supportteam](not sure) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet aktiverar du B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Hosted MyCirqa SSO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Hosted MyCirqa SSO**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-hosted-mycirqa-sso"></a>Konfigurera hosted MyCirqa SSO

Om du vill konfigurera enkel inloggning på **hosted MyCirqa SSO-sidan** måste du skicka **url:en-metadata för App Federation** till [MyCirqa SSO-supportteamet](mailto:support@isoxford.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Skapa värdenad MyCirqa SSO-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Hosted MyCirqa SSO. Arbeta med [Hosted MyCirqa SSO supportteam](mailto:support@isoxford.com) för att lägga till användare i Hosted MyCirqa SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Hosted MyCirqa SSO på åtkomstpanelen ska du automatiskt loggas in på den värdbaserade MyCirqa SSO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Hosted MyCirqa SSO med Azure AD](https://aad.portal.azure.com/)