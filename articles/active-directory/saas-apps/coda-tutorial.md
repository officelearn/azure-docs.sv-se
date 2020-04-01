---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Coda | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74da278dbbc0ac32407c345524e224ca5f7616da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77194756"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Coda

I den här självstudien får du lära dig hur du integrerar Coda med Azure Active Directory (Azure AD). När du integrerar Coda med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Coda.
* Gör att användarna automatiskt loggas in på Coda med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Coda enkel inloggning (SSO) aktiverad prenumeration (Enterprise) med GDrive-integrering inaktiverad. Kontakta [Codas supportteam](mailto:support@coda.io) för att inaktivera GDrive-integrering för din organisation om den är aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Coda stöder **IDP** initierad SSO

* Coda stöder just in time-användaretablering **Just In Time**

* När du har konfigurerat Coda kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Lägga till Coda från galleriet

Om du vill konfigurera integreringen av Coda i Azure AD måste du lägga till Coda från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Coda** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Coda** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Konfigurera och testa en azure AD-inloggning för Coda

Konfigurera och testa Azure AD SSO med Coda med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Coda.

Om du vill konfigurera och testa Azure AD SSO med Coda slutför du följande byggblock:

1. **[Börja konfigurationen av Coda SSO](#begin-configuration-of-coda-sso)** - för att påbörja konfigurationen av SSO i Coda.
1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
   * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Coda SSO](#configure-coda-sso)** - för att slutföra konfigurationen av enstaka inloggningsinställningar i Coda.
   * **[Skapa Coda-testanvändare](#create-coda-test-user)** – om du vill ha en motsvarighet till B.Simon i Coda som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="begin-configuration-of-coda-sso"></a>Börja konfigurationen av Coda SSO

Följ dessa steg i Coda för att börja.

1. Öppna panelen **Organisationsinställningar i** Coda.

   ![Öppna organisationsinställningar](media/coda-tutorial/org-settings.png)

1. Kontrollera att din organisation har inaktiverat GDrive-integrering. Om det är aktiverat kontaktar du [Codas supportteam](mailto:support@coda.io) för att hjälpa dig att migrera från GDrive.

   ![GDrive inaktiverat](media/coda-tutorial/gdrive-off.png)

1. Under **Autentisera med SSO (SAML)** väljer du alternativet **Konfigurera SAML.**

   ![Saml-inställningar](media/coda-tutorial/saml-settings-link.png)

1. Observera värdena för **entitets-ID** och **SAML-svars-URL**, som du behöver i efterföljande steg.

   ![URL för entitets-ID och SAML-svar som ska användas i Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

   a. I textrutan **Identifierare** anger du "Entitets-ID" ovanifrån. Det bör följa mönstret:`https://coda.io/samlId/<CUSTOMID>`

   b. I textrutan **Svara url** anger du "SAML Response URL" ovanifrån. Det bör följa mönstret:`https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Dina värden kommer att skilja sig från ovanstående; hittar du dina värden i Codas "Konfigurera SAML"-konsol. Uppdatera dessa värden med den faktiska identifieraren och svars-URL.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Coda** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Coda.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Coda**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-coda-sso"></a>Konfigurera Coda SSO

För att slutföra installationen anger du värden från Azure Active Directory på panelen Coda **Configure Saml.**

1. Öppna panelen **Organisationsinställningar i** Coda.
1. Under **Autentisera med SSO (SAML)** väljer du alternativet **Konfigurera SAML.**
1. Ange **SAML-provider** till **Azure Active Directory**.
1. Klistra in **inloggnings-URL:en i** **Identitetsproviderns inloggnings-URL**från Azure-konsolen.
1. I **Identity Provider Issuer**klistrar du in **Azure AD-identifieraren** från Azure-konsolen.
1. I **Public Certificate för identitetsprovider**väljer du alternativet Ladda upp **certifikat** och väljer den certifikatfil som du hämtade tidigare.
1. Välj **Spara**.

Detta slutför det arbete som krävs för SIML SSO-anslutningsinställningarna.

### <a name="create-coda-test-user"></a>Skapa Coda-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Coda. Coda stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Coda skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Coda-panelen på åtkomstpanelen ska du automatiskt loggas in på den Coda som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Coda med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Coda med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
