---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAP Cloud Platform Identity Authentication | Microsoft-dokument'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289075"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SAP Cloud Platform Identity Authentication

I den här självstudien får du lära dig hur du integrerar SAP Cloud Platform Identity Authentication med Azure Active Directory (Azure AD). När du integrerar SAP Cloud Platform Identity Authentication med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Cloud Platform Identity Authentication.
* Aktivera dina användare automatiskt inloggad på SAP Cloud Platform Identity Authentication med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SAP Cloud Platform Identity Authentication enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud Platform Identity Authentication stöder **SP**- och **IDP**-initierad enkel inloggning
* När du har konfigurerat SAP Cloud Platform Identity Authentication kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Innan du går in närmare på de tekniska detaljerna är det viktigt att förstå de begrepp som kommer att påträffa. Med SAP Cloud Platform Identity Authentication och Active Directory Federation Services kan du implementera enkel inloggning över flera program eller tjänster som skyddas av Azure AD (som IdP) med SAP-program och -tjänster som skyddas av SAP Cloud Platform Identity Authentication.

För närvarande fungerar SAP Cloud Platform Identity Authentication som Proxy Identity Provider (proxyidentitetsprovider) för SAP-program. Azure Active Directory fungerar i sin tur som ledande identitetsprovider i den här konfigurationen. 

Följande diagram illustrerar den här relationen:

![Skapa en testanvändare för Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Med den här konfigurationen är din klientorganisation för SAP Cloud Platform Identity Authentication konfigurerad som ett betrott program i Azure Active Directory.

Alla SAP-program och -tjänster som du vill skydda på det här sättet konfigureras sedan i hanteringskonsolen för SAP Cloud Platform Identity Authentication.

Därför behöver auktoriseringen för att bevilja åtkomst till SAP-program och -tjänster ske i SAP Cloud Platform Identity Authentication (i stället för i Azure Active Directory).

Genom att konfigurera SAP Cloud Platform Identity Authentication som ett program via Azure Active Directory Marketplace behöver du inte konfigurera enskilda anspråk eller SAML-intyg.

> [!NOTE]
> För närvarande har endast enkel inloggning på webben testats av båda parter. Flöden som är nödvändiga för kommunikation app-till-API eller API-till-app bör fungera men har inte testats ännu. De kommer att testas under senare aktiviteter.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägga till SAP Cloud Platform Identity Authentication från galleriet

För att konfigurera integrering av SAP Cloud Platform Identity Authentication med Azure AD behöver du lägga till SAP Cloud Platform Identity Authentication från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP Cloud Platform Identity Authentication** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **SAP Cloud Platform Identity Authentication** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Konfigurera och testa azure AD-enkel inloggning för SAP Cloud Platform Identity Authentication

Konfigurera och testa Azure AD SSO med SAP Cloud Platform Identity Authentication med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud Platform Identity Authentication.

Så här konfigurerar och testar du Azure AD SSO med SAP Cloud Platform Identity Authentication:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SAP Cloud Platform Identity Authentication SSO](#configure-sap-cloud-platform-identity-authentication-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa TESTANVÄNDARE](#create-sap-cloud-platform-identity-authentication-test-user)** för SAP Cloud Platform Identity Authentication - om du vill ha en motsvarighet till B.Simon i SAP Cloud Platform Identity Authentication som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan Integrering **av** SAP Cloud Platform Identity **Authentication-program**och väljer enkel inloggning . **SAP Cloud Platform Identity Authentication**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Gör följande i i **IDP-initierat**läge i avsnittet **Grundläggande SAML-konfiguration:**

    ![SAP Cloud Platform Identity Authentication-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`<IAS-tenant-id>.accounts.ondemand.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) och be om dessa värden. Om du inte förstår identifierarvärde läser du dokumentationen för SAP Cloud Platform Identity Authentication om [SAML 2.0-konfiguration för klientorganisation](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klicka på **Ange ytterligare webbadresser** och utför följande steg om du vill konfigurera programmet i **SP-initierat**läge:

    ![SAP Cloud Platform Identity Authentication-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Använd inloggnings-URL för ditt specifika affärsprogram. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) om du behöver hjälp.

1. SAP Cloud Platform Identity Authentication-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar till din SAML-tokenattributkonfiguration. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig SAP Cloud Platform Identity Authentication-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. I avsnittet **Konfigurera SAP Cloud Platform Identity Authentication** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud Platform Identity Authentication.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **SAP Cloud Platform Identity Authentication**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurera SSO för identitetsautentisering för SAP-molnplattformsidentitet

1. För att konfigurera enkel inloggning för programmet går du till administrationskonsolen för SAP Cloud Platform Identity Authentication. URL:en har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Läs sedan dokumentationen om SAP Cloud Platform Identity Authentication i avsnittet om [integrering med Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. I Azure-portalen väljer du knappen **Spara**.

3. Fortsätt endast med följande om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa stegen från avsnittet **Lägga till SAP Cloud Platform Identity Authentication från galleriet**.

4. I Azure-portalen går du till programintegreringssidan för **SAP Cloud Platform Identity Authentication** och väljer **Linked Sign-on** (Länkad inloggning).

    ![Konfigurera länkad inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Spara konfigurationen.

> [!NOTE]
> Det nya programmet använder konfigurationen för enkel inloggning från föregående SAP-program. Se till att du använder samma företagsidentitetsprovidrar (Corporate Identity Providers) i administrationskonsolen för SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare för SAP Cloud Platform Identity Authentication

Du behöver inte skapa en användare i SAP Cloud Platform Identity Authentication. Användare som finns i Azure AD-användararkivet kan använda funktionen för enkel inloggning.

SAP Cloud Platform Identity Authentication stöder alternativet för identitetsfederation. Det här alternativet gör att programmet kan kontrollera huruvida användare som autentiseras av företagsidentitetsprovidern finns i användararkivet för SAP Cloud Platform Identity Authentication.

Alternativet för identitetsfederation är inaktiverat som standard. Om identitetsfederation är aktiverat kan endast de användare som importeras i SAP Cloud Platform Identity Authentication få åtkomst till programmet.

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform Identity Authentication finns i ”Aktivera identitetsfederation med SAP Cloud Platform Identity Authentication” i avsnittet om att [konfigurera identitetsfederation med användararkivet för SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud Platform Identity Authentication-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Cloud Platform Identity Authentication som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Cloud Platform Identity Authentication med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du SAP Cloud Platform Identity Authentication med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
