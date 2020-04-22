---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med MongoDB Cloud | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MongoDB Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02cc78f7e786e97062cce6d402c972e66fa56860
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med MongoDB Cloud

I den här självstudien får du lära dig hur du integrerar MongoDB Cloud med Azure Active Directory (Azure AD). När du integrerar MongoDB Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MongoDB Cloud, MongoDB Atlas, MongoDB-communityn, MongoDB University och MongoDB Support.
* Aktivera dina användare så att de automatiskt loggas in i MongoDB Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En MongoDB Cloud-prenumeration som är aktiverad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* MongoDB Cloud stöder **SP** och **IDP** initierade SSO.
* MongoDB Cloud stöder just in time-användaretablering. **Just In Time**
* När du har konfigurerat MongoDB Cloud kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Lägg till MongoDB Cloud från galleriet

Om du vill konfigurera integreringen av MongoDB Cloud i Azure AD måste du lägga till MongoDB Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj Azure Active **Directory**i den vänstra rutan .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **MongoDB Cloud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **MongoDB Cloud** från resultaten och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Konfigurera och testa en azure AD-inloggning för MongoDB Cloud

Konfigurera och testa Azure AD SSO med MongoDB Cloud med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i MongoDB Cloud.

Så här konfigurerar och testar du Azure AD SSO med MongoDB Cloud:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. [Konfigurera MongoDB Cloud SSO](#configure-mongodb-cloud-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. [Skapa en MongoDB Cloud-testanvändare](#create-a-mongodb-cloud-test-user) för att ha en motsvarighet till B.Simon i MongoDB Cloud, länkad till Azure AD-representationen av användaren.
1. [Testa SSO](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Hitta avsnittet Hantera på sidan **MongoDB** Cloud-programintegrering på [Azure-portalen.](https://portal.azure.com/) **Manage** Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL som använder följande mönster i textrutan **Identifierare:**`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Skriv en URL som använder följande mönster i textrutan **Svara URL:**`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Välj **Ange ytterligare webbadresser**och utför följande steg om du vill konfigurera programmet i **återupptan** av SP-initierat läge:

    Skriv en URL som använder följande mönster i textrutan **Sign-on-URL:**`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med url:en för faktisk identifierare, svar och inloggning. Kontakta supportteamet för [MongoDB Cloud Client](https://support.mongodb.com/)för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. MongoDB Cloud-programmet förväntar sig att SAML-påståendena är i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Skärmbild av standardattribut](common/default-attributes.png)

1. Förutom de föregående attributen förväntar sig MongoDB Cloud-programmet att några fler attribut skickas tillbaka i SAML-svaret. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Namn | |  Källattribut|
    | ---------------| --------------- | --------- |
    | e-post | | user.userprincipalname |
    | firstName | | user.givenname |
    | lastName | | user.surname |

1. Leta reda på XML för **federationsmetadata**i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML.** Välj **Hämta** om du vill hämta certifikatet och spara det på datorn.

    ![Skärmbild av avsnittet SAML-signeringscertifikat, med länken Hämta markerad](common/metadataxml.png)

1. I avsnittet **Konfigurera MongoDB Cloud** kopierar du lämpliga webbadresser baserat på dina behov.

    ![Skärmbild av avsnittet Konfigurera Mongo DB Cloud, med webbadresser markerade](common/copy-configuration-urls.png)
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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MongoDB Cloud.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. Välj **MongoDB Cloud**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av sidan Användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. Välj **B.Simon** i listan över användare **och grupper.** Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurera MongoDB Cloud SSO

Om du vill konfigurera enkel inloggning på MongoDB Cloud-sidan behöver du lämpliga url:er som kopieras från Azure-portalen. Du måste också konfigurera federationsprogrammet för din MongoDB Cloud Organization. Följ instruktionerna i [MongoDB Cloud-dokumentationen](https://docs.atlas.mongodb.com/security/federated-authentication/index.html). Om du har problem kontaktar du Supportteamet för [MongoDB Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Skapa en MongoDB Cloud-testanvändare

MongoDB Cloud stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns ingen ytterligare åtgärd för dig att vidta. Om en användare inte redan finns i MongoDB Cloud skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av Åtkomstpanelen.

När du väljer panelen MongoDB Cloud på Åtkomstpanelen loggas du automatiskt in i MongoDB-molnet som du konfigurerar SSO för. Mer information finns [i Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Registrera dig för MongoDB Atlas på Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Prova MongoDB Cloud med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda MongoDB Cloud med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

