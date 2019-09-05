---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med JAMF Pro | Microsoft Docs'
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
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305448"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med JAMF Pro

I den här självstudien får du lära dig att integrera JAMF Pro med Azure Active Directory (Azure AD). När du integrerar JAMF Pro med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till JAMF Pro.
* Gör det möjligt för användarna att logga in automatiskt till JAMF Pro med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* JAMF Pro-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Jamf Pro har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-jamf-pro-from-the-gallery"></a>Lägga till Jamf Pro från galleriet

För att konfigurera integrering av Jamf Pro med Azure AD behöver du lägga till Jamf Pro från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **JAMF Pro** i sökrutan.
1. Välj **JAMF Pro** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Konfigurera och testa enkel inloggning med Azure AD för JAMF Pro

Konfigurera och testa Azure AD SSO med JAMF Pro med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i JAMF Pro.

Om du vill konfigurera och testa Azure AD SSO med JAMF Pro slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera JAMF Pro SSO](#configure-jamf-pro-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa JAMF Pro test User](#create-jamf-pro-test-user)** – om du vill ha en motsvarighet till B. Simon i JAMF Pro som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för integrering av **JAMF Pro** -program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får det faktiska identifierarvärdet från avsnittet **Enkel inloggning** i Jamf Pro-portalen, vilket beskrivs senare i självstudien. Du kan extrahera det faktiska värdet för **underdomän** från identifierarvärdet och använda den informationen om **underdomän** i inloggnings-URL och svars-URL. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till JAMF Pro.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Jamf Pro** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-jamf-pro-sso"></a>Konfigurera JAMF Pro SSO

1. Om du vill automatisera konfigurationen i Jamf Pro måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera Jamf Pro**. Du omdirigeras då till Jamf Pro-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Jamf Pro. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera JAMF Pro manuellt öppnar du ett nytt webbläsarfönster och loggar in på din JAMF Pro-företags webbplats som administratör och utför följande steg:

4. Klicka på **ikonen för Inställningar** i det övre högra hörnet på sidan.

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Klicka på **Enkel inloggning**.

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. På sidan **Enkel inloggning** utför du följande steg:

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Kontrol lera **autentiseringen aktivera enkel inloggning**.

    b. Välj **annat** som alternativ i list rutan **identitets leverantör** .

    c. I textrutan **OTHER PROVIDER** (Annan provider) anger du **Azure AD**.

    d. Kopiera **entitets-ID-** värdet och klistra in det i text rutan **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    > [!NOTE]
    > Här `<SUBDOMAIN>` måste du använda det här värdet för att slutföra inloggnings-URL: en och svars-URL: en i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    e. Välj **URL för metadata** som ett alternativ i list rutan för **metadata källa för identitetsprovider** och i följande text ruta klistrar du in URL-värdet för **appens federationens metadata** som du har kopierat från Azure Portal.

7. På samma sida rullar du upp till **användar mappnings** avsnittet och utför följande steg: 

    ![Jamf Pro enkel](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Välj alternativet **NameID** för **identitets leverantörens användar mappning**. Som standard är den här inställningen angiven till **NameID**, men du kan definiera ett anpassat attribut.

    b. Välj **e-post** för **användar MAPPNING av JAMF Pro**. Jamf Pro mappar SAML-attribut som skickas av IdP på följande sätt: via användare och via grupper. När en användare försöker ansluta till Jamf Pro hämtar Jamf Pro som standard information om användaren från identitetsprovidern och jämför den med Jamf Pro-användarkonton. Om det inkommande användarkontot inte finns i Jamf Pro sker matchning av gruppnamn.

    c. Klistra in värdet `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` i text rutan **namn på grupp för identitetsprovider** .

    d. Genom att välja **Tillåt användare att kringgå autentisera användare för enkel inloggning** omdirigeras de inte till inloggnings sidan för identitetsprovider för autentisering, men kan logga in på JAMF Pro direkt i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern sker görs IdP-initierad autentisering och auktorisering med enkel inloggning.

    e. Klicka på **Spara**.

### <a name="create-jamf-pro-test-user"></a>Skapa Jamf Pro-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på JAMF Pro måste de tillhandahållas i JAMF Pro. För Jamf Pro är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din företags webbplats för JAMF Pro som administratör.

2. Klicka på **ikonen för Inställningar** i det övre högra hörnet på sidan.

    ![Lägga till medarbetare](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klicka på **Jamf Pro User Accounts & Groups** (Jamf Pro-användarkonto och grupper).

    ![Lägga till medarbetare](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klicka på **Ny**.

    ![Lägga till medarbetare](./media/jamfprosamlconnector-tutorial/user2.png)

5. Välj **Create Standard Account** (Skapa standardkonto).

    ![Lägga till medarbetare](./media/jamfprosamlconnector-tutorial/user3.png)

6. I dialogrutan **Nytt konto** utför du följande steg:

    ![Lägga till medarbetare](./media/jamfprosamlconnector-tutorial/user4.png)

    a. I textrutan **USERNAME** (Användarnamn) skriver du det fullständiga namnet BrittaSimon.

    b. Välj lämpliga alternativ enligt din organisation för **ACCESS LEVEL** (Åtkomstnivå), **PRIVILEGE SET** (Behörighetsinställning) och **ACCESS STATUS** (Åtkomststatus).

    c. I textrutan **FULL NAME** (Fullständigt namn) skriver du det fullständiga namnet BrittaSimon.

    d. I textrutan **EMAIL ADDRESS** (E-postadress) anger du e-postadressen för Britta Simon-kontot.

    e. I textrutan **PASSWORD** (Lösenord) skriver du lösenordet för användaren.

    f. I textrutan **VERIFY PASSWORD** (Verifiera lösenord) skriver du lösenordet för användaren.

    g. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Jamf Pro-panelen i åtkomstpanelen bör du automatiskt loggas in på Jamf Pro som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa JAMF Pro med Azure AD](https://aad.portal.azure.com/)

