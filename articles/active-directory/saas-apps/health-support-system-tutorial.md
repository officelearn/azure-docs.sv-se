---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Health Support System | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Health Support System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96f35ded-07bc-46f0-87df-fe88fcc367cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf449880c1a7d1fdd1f951bf5ecbb7316efb8e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76992340"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-health-support-system"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med hälsostödsystem

I den här självstudien får du lära dig hur du integrerar hälsostödsystem med Azure Active Directory (Azure AD). När du integrerar hälsostödsystem med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till hälsosupportsystem.
* Gör att användarna automatiskt loggas in i hälsostödsystemet med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SSO-prenumeration (Health Support System single sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Hälsosupportsystem stöder **SP** initierade SSO

* När du har konfigurerat hälsostödsystemet kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-health-support-system-from-the-gallery"></a>Lägga till hälsostöd från galleriet

Om du vill konfigurera integreringen av hälsostödssystem i Azure AD måste du lägga till hälsostöd från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Hälsostödsystem** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Hälsostödsystem** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-health-support-system"></a>Konfigurera och testa Azure AD enkel inloggning för hälsosupportsystem

Konfigurera och testa Azure AD SSO med hälsosupportsystem med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i hälsosupportsystemet.

Så här konfigurerar och testar du Azure AD SSO med hälsosupportsystem:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SSO för hälsostödsystem](#configure-health-support-system-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa hälsosupportsystemtestanvändare](#create-health-support-system-test-user)** – om du vill ha en motsvarighet till B.Simon i hälsostödsystemet som är länkat till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan Integrering av programintegrering av **hälsostödssystem** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Skriv **Basic SAML Configuration** URL:en i textrutan **Sign-on-URL: Skriv URL:en** i textrutan Sign-on URL:`https://suntory.karakoko.jp`
    
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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till hälsostödsystemet.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Hälsostödsystem**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-health-support-system-sso"></a>Konfigurera SSO för hälsostödsystem

Om du vill konfigurera enkel inloggning på **hälsosupportsystemsidan** måste du skicka **url:en för appfederationsmetadata** till [supportteamet för hälsostöd.](https://wellcoms.jp/inquiry/) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-health-support-system-test-user"></a>Skapa användare av hälsostödsystem

I det här avsnittet skapar du en användare som heter B.Simon i hälsostödsystemet. Arbeta med supportteamet för [hälsostöd för](https://wellcoms.jp/inquiry/) att lägga till användarna i hälsosupportsystemplattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Hälsostödssystem på åtkomstpanelen bör du automatiskt loggas in i det hälsostödsystem som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova hälsosupportsystem med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du hälsostödssystemet med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)