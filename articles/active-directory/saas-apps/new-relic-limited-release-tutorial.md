---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med New Relic (Limited Release) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic (Limited Release).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ffbcf4a-b6b4-45ac-922f-358abe4c05e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad229e7964b6e7ad0e3f0d58cfc09669ee2fb301
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088202"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-limited-release"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Ny Relik (Begränsad utgåva)

I den här självstudien får du lära dig hur du integrerar Ny Relik (begränsad version) med Azure Active Directory (Azure AD). När du integrerar Ny relik (begränsad version) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Ny Relik (begränsad version).
* Gör att användarna automatiskt loggas in på Ny relik (begränsad version) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Ny relik (begränsad frisläppande) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Ny Relic (Limited Release) stöder **IDP** initierad SSO

* När du har konfigurerat den nya reliken kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-limited-release-from-the-gallery"></a>Lägga till ny relik (limited release) från galleriet

Om du vill konfigurera integreringen av Ny Relik (begränsad version) i Azure AD måste du lägga till Ny relik (begränsad version) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Ny relik (begränsad utgivning)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Ny relik (begränsad version)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-limited-release"></a>Konfigurera och testa en enda Azure AD-inloggning för ny relik (begränsad utgåva)

Konfigurera och testa Azure AD SSO med Ny Relik (begränsad version) med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Ny relik (begränsad version).

Så här konfigurerar och testar du Azure AD SSO med New Relic (Limited Release) och slutför följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SSO för ny relik (begränsad utgåva)](#configure-new-relic-limited-release-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa testanvändare för ny relik (begränsad utgåva)](#create-new-relic-limited-release-test-user)** – om du vill ha en motsvarighet till B.Simon i ny relik (begränsad version) som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På [Azure portal](https://portal.azure.com/)sidan **Ny relik (begränsad version)** på sidan För integrering av nya reliker (begränsad version) letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts fylls **URL-värdena identifierare** och **svar** i i fyllas i automatiskt i avsnittet Grundläggande SAML-konfiguration:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ny relik (begränsad version).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Ny relik (begränsad version)** i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-new-relic-limited-release-sso"></a>Konfigurera ny relik (begränsad utgåva) SSO

Om du vill konfigurera enkel inloggning på sidan **Ny relik (begränsad utgåva)** måste du skicka **appfederationens metadataadress** till [supportteamet för nya reliker (begränsad publicering).](https://support.newrelic.com/) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.


### <a name="create-new-relic-limited-release-test-user"></a>Skapa testanvändare för ny relik (begränsad frisläppning)

I det här avsnittet skapar du en användare som heter B.Simon i Ny relik (begränsad version). Arbeta med  [supportteamet för Nya Reliker (Limited Release)](https://support.newrelic.com/)för att lägga till användarna i den nya relikplattformen (Limited Release). Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Ny relik (begränsad utgivning) på åtkomstpanelen bör du automatiskt loggas in på den nya relik (begränsad version) som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ny relik (begränsad version) med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)