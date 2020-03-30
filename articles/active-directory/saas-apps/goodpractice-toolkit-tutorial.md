---
title: 'Självstudiekurs: Azure Active Directory-integrering med Mind Tools Toolkit | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mind Tools Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b12bd8ba7998b924035a0946f9e32b88ce206e4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79476509"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Självstudiekurs: Azure Active Directory-integrering med Mind Tools Toolkit

I den här självstudien får du lära dig hur du integrerar Mind Tools Toolkit med Azure Active Directory (Azure AD).
Genom att integrera Verktygslåda för mind tools med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mind Tools Toolkit.
* Du kan aktivera användarna automatiskt inloggad på Mind Tools Toolkit (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Mind Tools Toolkit behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Mind Tools Toolkit enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mind Tools Toolkit stöder **SP** initierade SSO
* Mind Tools Toolkit stöder just in time-användaretablering **Just In Time**
* När du har konfigurerat Mind Tools Toolkit kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mind-tools-toolkit-from-the-gallery"></a>Lägga till Verktygslåda för Mind Tools från galleriet

Om du vill konfigurera integreringen av Mind Tools Toolkit i Azure AD måste du lägga till Mind Tools Toolkit från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Verktygslåda för Verktyg** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Verktygslåda för Mind Tools** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Mind Tools Toolkit baserat på en testanvändare som heter **B.Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mind Tools Toolkit upprättas.

Om du vill konfigurera och testa Azure AD-enkel inloggning med Mind Tools Toolkit måste du slutföra följande byggstenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Mind Tools Toolkit SSO](#configure-mind-tools-toolkit-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Mind Tools Toolkit testanvändare](#create-mind-tools-toolkit-test-user)** - att ha en motsvarighet till B.Simon i Mind Tools Toolkit som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med Mind Tools Toolkit:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Mind Tools Toolkit-programprogram.** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`.

    > [!Note]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Mind Tools Toolkit Client support team](mailto:support@goodpractice.com) för att få värdet.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera verktygsverktyg.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mind Tools Toolkit.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Verktygslåda för Mind Tools**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-mind-tools-toolkit-sso"></a>Konfigurera Verktygslåda för tankeverktyg SSO

Om du vill konfigurera enkel inloggning på **Mind Tools Toolkit-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade url:er från Azure-portalen till [Mind Tools Toolkit supportteam](mailto:support@goodpractice.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-mind-tools-toolkit-test-user"></a>Skapa verktygsverktygstestanvändare

I det här avsnittet skapas en användare som heter B.Simon i Mind Tools Toolkit. Mind Tools Toolkit stöder **just-in-time-etablering**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Mind Tools Toolkit skapas en ny när du försöker komma åt Mind Tools Toolkit.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Verktygslåda för mind tools på åtkomstpanelen bör du automatiskt loggas in i verktygslådan För tankeverktyg som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Mind Tools Toolkit med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Mind Tools Toolkit med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)