---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med EAB Navigate Strategic Care | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EAB Navigate Strategic Care.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0c61fcfc-b89b-4f89-9b81-27098047cddb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1185217d6b12d3efd5dedc9faf903f1b365481db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70014071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med EAB Navigate Strategic Care

I den här självstudien får du lära dig hur du integrerar EAB Navigate Strategic Care med Azure Active Directory (Azure AD). När du integrerar EAB Navigate Strategic Care med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EAB Navigate Strategic Care.
* Gör att användarna automatiskt loggas in på EAB Navigate Strategic Care med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* EAB Navigate Strategic Care enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* EAB Navigate Strategic Care stödjer **SP** initierade SSO

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>Lägga till EAB Navigate Strategic Care från galleriet

Om du vill konfigurera integreringen av EAB Navigate Strategic Care i Azure AD måste du lägga till EAB Navigate Strategic Care från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **EAB Navigate Strategic Care** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **EAB Navigera strategisk vård** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Konfigurera och testa azure AD enkel inloggning för EAB Navigate Strategic Care

Konfigurera och testa Azure AD SSO med EAB Navigate Strategic Care med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i EAB Navigate Strategic Care.

Så här konfigurerar och testar du Azure AD SSO med EAB Navigate Strategic Care:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera EAB Navigate Strategic Care SSO](#configure-eab-navigate-strategic-care-sso)** - för att konfigurera de enskilda inloggningsinställningarna på applikationssidan.
    1. **[Skapa EAB Navigate Strategic Care testanvändare](#create-eab-navigate-strategic-care-test-user)** - att ha en motsvarighet till B.Simon i EAB Navigate Strategic Care som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **EAB Navigate Strategic** Care-programintegrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EAB Navigera Strategisk vård Kundtjänst supportteam](mailto:tech@gradesfirst.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på kopieringsknappen i avsnittet **SAML-signeringscertifikat** i sidan **Konfigurera enkel inloggning med SAML** för att kopiera Url till App **Federationsmetadata** och spara den på datorn.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till EAB Navigate Strategic Care.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **EAB Navigate Strategic Care**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-eab-navigate-strategic-care-sso"></a>Konfigurera EAB Navigera strategisk vård SSO

Om du vill konfigurera enkel inloggning på **EAB Navigate Strategic Care-sidan** måste du skicka **appfederationens metadataadress** till [EAB Navigate Strategic Care support team](mailto:tech@gradesfirst.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-eab-navigate-strategic-care-test-user"></a>Skapa EAB Navigate Strategic Care testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i EAB Navigate Strategic Care. Arbeta med [EAB Navigate Strategic Care supportteam](mailto:tech@gradesfirst.com) för att lägga till användarna i EAB Navigate Strategic Care-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen EAB Navigate Strategic Care i åtkomstpanelen ska du automatiskt loggas in på den EAB Navigate Strategic Care som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova EAB Navigate Strategic Care med Azure AD](https://aad.portal.azure.com/)

