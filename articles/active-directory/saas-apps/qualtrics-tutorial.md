---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAP Qualtrics | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682378"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SAP Qualtrics

I den här självstudien får du lära dig hur du integrerar SAP Qualtrics med Azure Active Directory (Azure AD). När du integrerar SAP Qualtrics med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Qualtrics.
* Gör att användarna automatiskt loggas in på SAP Qualtrics med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En SAP Qualtrics-prenumeration aktiverad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SAP Qualtrics stöder **SP** och **IDP** initierade SSO.
* SAP Qualtrics stöder just in time-användaretablering. **Just In Time**
* När du har konfigurerat SAP Qualtrics kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>Lägg till SAP Qualtrics från galleriet

Om du vill konfigurera integreringen av SAP Qualtrics i Azure AD måste du lägga till SAP Qualtrics från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj Azure Active **Directory**i den vänstra rutan .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP Qualtrics** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SAP Qualtrics** från resultat och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Konfigurera och testa en azure AD-inloggning för SAP Qualtrics

Konfigurera och testa Azure AD SSO med SAP Qualtrics med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i SAP Qualtrics.

Så här konfigurerar och testar du Azure AD SSO med SAP Qualtrics:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. [Konfigurera SAP Qualtrics SSO](#configure-sap-qualtrics-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. [Skapa en SAP Qualtrics-testanvändare](#create-sap-qualtrics-test-user) för att ha en motsvarighet till B.Simon i SAP Qualtrics, länkad till Azure AD-representationen av användaren.
1. [Testa SSO](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **SAP Qualtrics-programintegration.** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge anger du värdena för följande fält på sidan **Konfigurera enkel inloggning med SAML:**
    
    a. Skriv en URL som använder följande mönster i textrutan **Identifierare:**

    `https://< DATACENTER >.qualtrics.com`
   
    b. Skriv en URL som använder följande mönster i textrutan **Svara URL:**

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Skriv en URL som använder följande mönster i textrutan **Återlämningstillstånd:**

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Välj **Ange ytterligare webbadresser**och utför följande steg om du vill konfigurera programmet i **återupptan** av SP-initierat läge:

    Skriv en URL som använder följande mönster **i textrutan Sign-on URL:**

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en, identifieraren, svars-URL:en och Relay State. Kontakta supportteamet för [Qualtrics Client](https://www.qualtrics.com/support/)för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du kopieringsikonen för att kopiera **Url till App Federation Metadata** och spara den på datorn.

    ![Skärmbild av SAML-signeringscertifikat, med kopieringsikonen markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** > .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned lösenordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Qualtrics.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **SAP Qualtrics**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i listan över användare **och grupper.** Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurera SAP Qualtrics SSO

Om du vill konfigurera enkel inloggning på SAP Qualtrics-sidan skickar du den kopierade **url:en-url till metadata** för App Federation från Azure-portalen till [SAP Qualtrics-supportteamet](https://www.qualtrics.com/support/). Supportteamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-sap-qualtrics-test-user"></a>Skapa SAP Qualtrics testanvändare

SAP Qualtrics stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns ingen ytterligare åtgärd för dig att vidta. Om en användare inte redan finns i SAP Qualtrics skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer sap qualtrics-panelen i Åtkomstpanelen loggas du automatiskt in på den SAP-qualtrics som du konfigurerar SSO för. Mer information finns [i Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Qualtrics med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda SAP Qualtrics med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

