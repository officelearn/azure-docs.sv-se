---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Jamf Pro | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77373183"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Självstudiekurs: Azure Active Directory SSO-integrering med Jamf Pro

I den här självstudien får du lära dig hur du integrerar Jamf Pro med Azure Active Directory (Azure AD). När du integrerar Jamf Pro med Azure AD kan du:

* Använd Azure AD för att styra vem som har åtkomst till Jamf Pro.
* Logga in automatiskt dina användare på Jamf Pro med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Jamf Pro-prenumeration som är enkel inloggning (SSO) aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

* Jamf Pro stöder **SP-initierad** och **IdP-initierad** SSO.
* När du har konfigurerat Jamf Pro kan du framtvinga Sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Lägg till Jamf Pro från galleriet

För att konfigurera integrering av Jamf Pro med Azure AD behöver du lägga till Jamf Pro från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ditt personliga Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i den vänstra rutan.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. I avsnittet **Lägg till från galleriet** anger du *Jamf Pro* i sökrutan.
1. Välj **Jamf Pro** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurera och testa SSO i Azure AD för Jamf Pro

Konfigurera och testa Azure AD SSO med Jamf Pro med hjälp av en testanvändare som heter B.Simon. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Jamf Pro.

I det här avsnittet konfigurerar och testar du Azure AD SSO med Jamf Pro.

1. [Konfigurera SSO i Azure AD](#configure-sso-in-azure-ad) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med B.Simon-kontot.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att B.Simon kan använda SSO i Azure AD.
1. [Konfigurera SSO i Jamf Pro](#configure-sso-in-jamf-pro) för att konfigurera SSO-inställningarna på programsidan.
    1. [Skapa en Jamf Pro-testanvändare](#create-a-jamf-pro-test-user) för att ha en motsvarighet till B.Simon i Jamf Pro som är länkad till Azure AD-representationen för användaren.
1. [Testa SSO-konfigurationen](#test-the-sso-configuration) för att kontrollera att konfigurationen fungerar.

## <a name="configure-sso-in-azure-ad"></a>Konfigurera SSO i Azure AD

I det här avsnittet aktiverar du Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Jamf Pro-programintegrering** på **Manage** [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera sidan Grundläggande SAML-konfiguration.](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IdP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Ange en URL som använder följande formel i textrutan **Identifierare:**`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Ange en URL som använder följande formel i textrutan **Svara URL:**`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Välj **Ange ytterligare webbadresser**. Om du vill konfigurera programmet i **SP-initierat** läge anger du en URL som använder följande formel i textrutan **Sign-on URL:**`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren, svars-URL:en och inloggnings-URL:en. Du får det faktiska identifierare värdet från avsnittet Enkel inloggning i Jamf **Pro-portalen,** som förklaras senare i självstudien. Du kan extrahera det faktiska underdomänvärdet från identifierare värdet och använda den underdomäninformationen som din inloggnings-URL och svars-URL. Du kan också referera till de formler som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat,** väljer **kopieringsknappen** för att kopiera **URL:en för App Federation Metadata**och spara den sedan på datorn.

    ![Nedladdningslänken för SAML-signeringscertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen som heter B.Simon.

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**, väljer **Användare**och väljer sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.
   1. Ange [namn]@[companydomain] i fältet **Användarnamn.** [förlängning]. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ger du B.Simon tillgång till Jamf Pro.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Jamf Pro** i programlistan.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Välj knappen Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och väljer sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i dialogrutan **Välj roll.** Välj sedan knappen **Välj** längst ned på skärmen.
1. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sso-in-jamf-pro"></a>Konfigurera SSO i Jamf Pro

1. Om du vill automatisera konfigurationen i Jamf Pro installerar du **webbläsartillägget My Apps Secure Sign-in** genom att välja **Installera tillägget**.

    ![Sidan För säker inloggning i mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren väljer du **Konfigurera Jamf Pro**. När Jamf Pro-programmet öppnas anger du administratörsautentiseringsuppgifterna för inloggning. Webbläsartillägget konfigurerar automatiskt programmet och automatiserar steg 3 till 7.

    ![Konfigurationssida för installation i Jamf Pro](common/setup-sso.png)

3. Om du vill konfigurera Jamf Pro manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Jamf Pro-företagswebbplats som administratör. Följ sedan följande steg.

4. Välj **ikonen Inställningar** längst upp till höger på sidan.

    ![Välj inställningsikonen i Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Välj **Enkel inloggning**.

    ![Välj Enkel inloggning i Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Gör följande på sidan **Enkel inloggning.**

    ![Sidan Enkel inloggning i Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **Redigera**.

    b. Markera kryssrutan **Aktivera enkel inloggningsautentisering.**

  c. Välj **Azure** som ett alternativ på den nedrullningsbara menyn **Identitetsprovider.**

  d. Kopiera **ENTITY ID-värdet** och klistra in det i fältet **Identifierare (Entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

> [!NOTE]
> Använd värdet i `<SUBDOMAIN>` fältet för att slutföra inloggnings-URL:en och svars-URL:en i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

  e. Välj **metadata-URL** på den nedrullningsbara menyn För metadatakälla för **identitetsprovider.** I fältet som visas klistrar du in **url-värdet för appfederationens metadata** som du har kopierat från Azure-portalen.

  f. (Valfritt) Redigera tokenförfallovärdet eller välj "Inaktivera START-tokenförfallodatum".

7. På samma sida bläddrar du ned till avsnittet **Användarmappning.** Följ sedan följande steg.

    ![Avsnittet Användarmappning på sidan Enkel inloggning i Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Välj **alternativet NameID** för **användarmappning av identitetsprovider**. Som standard är det här alternativet inställt på **NameID**, men du kan definiera ett anpassat attribut.

    b. Välj **E-post** för **Jamf Pro-användarmappning**. Jamf Pro mappar SAML-attribut som skickas av IdP först av användare och sedan av grupper. När en användare försöker komma åt Jamf Pro får Jamf Pro information om användaren från identitetsprovidern och matchar den mot alla Jamf Pro-användarkonton. Om det inkommande användarkontot inte hittas försöker Jamf Pro matcha det efter gruppnamn.

    c. Klistra in `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` värdet i fältet **ATTRIBUTNAMN FÖR IDENTITETSPROVIDERGRUPP.**

    d. På samma sida bläddrar du ned till avsnittet **Säkerhet** och väljer **Tillåt användare att kringgå autentiseringen med enkel inloggning**. Därför omdirigeras inte användare till inloggningssidan för identitetsprovider för autentisering och kan logga in på Jamf Pro direkt i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern sker görs IdP-initierad autentisering och auktorisering med enkel inloggning.

    e. Välj **Spara**.

### <a name="create-a-jamf-pro-test-user"></a>Skapa en Jamf Pro-testanvändare

För att Azure AD-användare ska kunna logga in på Jamf Pro måste de etableras i Jamf Pro. Etablering i Jamf Pro är en manuell aktivitet.

Så här etablerar du ett användarkonto:

1. Logga in på din Jamf Pro-företagswebbplats som administratör.

2. Välj ikonen **Inställningar** längst upp till höger på sidan.

    ![Inställningsikonen i Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Välj **Jamf Pro-användarkonton & grupper**.

    ![Ikonen Jamf Pro-användarkonton & grupper i Jamf Pro-inställningarna](./media/jamfprosamlconnector-tutorial/user1.png)

4. Välj **Ny**.

    ![Sidan Jamf Pro-användarkonton & systeminställningar för grupper](./media/jamfprosamlconnector-tutorial/user2.png)

5. Välj **Create Standard Account** (Skapa standardkonto).

    ![Alternativet Skapa standardkonto i sidan Jamf Pro-användarkonton & grupper](./media/jamfprosamlconnector-tutorial/user3.png)

6. Gör följande i dialogrutan **Nytt konto:**

    ![Nya kontoinställningsalternativ i Jamf Pro-systeminställningar](./media/jamfprosamlconnector-tutorial/user4.png)

    a. I fältet **ANVÄNDARNAMN** `Britta Simon`anger du , testanvändarens fullständiga namn.

    b. Välj alternativ för **ÅTKOMSTNIVÅ,** **BEHÖRIGHETSUPPSÄTTNING**och **ÅTKOMSTSTATUS** som är i enlighet med din organisation.

    c. Ange i fältet FULLSTÄNDIGT `Britta Simon` **NAMN** .

    d. Ange e-postadressen till Britta Simons konto i fältet **E-postadress.**

    e. Ange användarens lösenord i fältet **LÖSENORD.**

    f. Ange användarens lösenord igen i fältet **VERIFIERA LÖSENORD.**

    g. Välj **Spara**.

## <a name="test-the-sso-configuration"></a>Testa SSO-konfigurationen

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen Jamf Pro på åtkomstpanelen ska du automatiskt loggas in på det Jamf Pro-konto som du har konfigurerat SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för integrering av SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Prova Jamf Pro med Azure AD](https://aad.portal.azure.com/)