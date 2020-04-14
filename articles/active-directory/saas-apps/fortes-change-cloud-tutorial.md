---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Fortes Change Cloud | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fortes Change Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1590757f-ce9f-4066-911f-47f3d3b92443
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55305fff95e9707b35ea265915e2893a736ab97d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortes-change-cloud"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Fortes Change Cloud

I den här självstudien får du lära dig hur du integrerar Fortes Change Cloud med Azure Active Directory (Azure AD). När du integrerar Fortes Change Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Fortes Change Cloud.
* Aktivera dina användare så att de automatiskt loggas in på Fortes Change Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Fortes Ändra Cloud enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Fortes Change Cloud stöder **SP och IDP** initierad SSO
* När du har konfigurerat Fortes Change Cloud kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortes-change-cloud-from-the-gallery"></a>Lägga till Fortes Ändra moln från galleriet

Om du vill konfigurera integreringen av Fortes Change Cloud i Azure AD måste du lägga till Fortes Change Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Fortes Change Cloud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Fortes Ändra moln** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-fortes-change-cloud"></a>Konfigurera och testa Azure AD enkel inloggning för Fortes Change Cloud

Konfigurera och testa Azure AD SSO med Fortes Change Cloud med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Fortes Change Cloud.

Så här konfigurerar och testar du Azure AD SSO med Fortes Change Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Fortes Change Cloud SSO](#configure-fortes-change-cloud-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Fortes Change Cloud testanvändare](#create-fortes-change-cloud-test-user)** - att ha en motsvarighet till B.Simon i Fortes Change Cloud som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj enkel inloggning på sidan **Förändrar molnprogram** och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<identifier>.fortes-online.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<identifier>.fortes-online.com/saml/SSO`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<identifier>.fortes-online.com/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Fortes Change Cloud Client support team](mailto:support@fortes.nl) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Fortes Change Cloud-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Fortes Change Cloud-programmet förväntar sig att **namnidentifieraren** ska mappas med **user.samaccountname**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fortes Change Cloud.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Fortes Change Cloud**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-fortes-change-cloud-sso"></a>Konfigurera Fortes Ändra Cloud SSO

Om du vill konfigurera enkel inloggning på **Fortes Change Cloud-sidan** måste du skicka **url:en för appfederationsmetadata** till [Fortes Change Cloud-supportteamet](mailto:support@fortes.nl). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-fortes-change-cloud-test-user"></a>Skapa Fortes Ändra cloudtestanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Fortes Change Cloud. Arbeta med [Fortes Change Cloud supportteam](mailto:support@fortes.nl) för att lägga till användare i Fortes Change Cloud-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Fortes Change Cloud på åtkomstpanelen bör du automatiskt loggas in i det Fortes Change-moln som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Fortes Change Cloud med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Fortes Change Cloud med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

