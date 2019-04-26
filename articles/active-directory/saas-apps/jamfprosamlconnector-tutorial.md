---
title: 'Självstudier: Azure Active Directory-integrering med Jamf Pro | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b575b74c80499f2ddd6648bf051b5245077d2f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60269494"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Självstudier: Azure Active Directory-integrering med Jamf Pro

I den här självstudien lär du dig att integrera Jamf Pro med Azure Active Directory (AD Azure).
Genom att integrera Jamf Pro med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Jamf Pro.
* Du kan göra så att dina användare automatiskt loggas in på Jamf Pro (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med Jamf Pro:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Jamf Pro-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Jamf Pro har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-jamf-pro-from-the-gallery"></a>Lägga till Jamf Pro från galleriet

För att konfigurera integrering av Jamf Pro med Azure AD behöver du lägga till Jamf Pro från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jamf Pro från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Jamf Pro**, väljer **Jamf Pro** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Jamf Pro i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Jamf Pro baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Jamf Pro upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Jamf Pro:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Jamf Pro](#configure-jamf-pro-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Jamf Pro-testanvändare](#create-jamf-pro-test-user)** – för att ha en motsvarighet till Britta Simon i Jamf Pro som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Jamf Pro:

1. På [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Jamf Pro** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera i **IDP**-initierat läge:

    ![Jamf Pro-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/SSO`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.jamfcloud.com`

    ![Jamf Pro-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får det faktiska identifierarvärdet från avsnittet **Enkel inloggning** i Jamf Pro-portalen, vilket beskrivs senare i självstudien. Du kan extrahera det faktiska värdet för **underdomän** från identifierarvärdet och använda den informationen om **underdomän** i inloggnings-URL och svars-URL. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Konfigurera enkel inloggning för Jamf Pro

1. Om du vill automatisera konfigurationen i Jamf Pro måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera Jamf Pro**. Du omdirigeras då till Jamf Pro-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Jamf Pro. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Om du vill konfigurera Jamf Pro manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Jamf Pro-företagsplats som administratör och utför följande steg:

4. Klicka på **ikonen för Inställningar** i det övre högra hörnet på sidan.

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Klicka på **Enkel inloggning**.

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. På sidan **Enkel inloggning** utför du följande steg:

    ![Jamf Pro enkel](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Välj **Jamf Pro Server** för att aktivera åtkomst via enkel inloggning.

    b. När du väljer **Allow bypass for all users** (Tillåt förbikoppling för alla användare) omdirigeras inte användarna till identitetsproviderns inloggningssida för autentisering, utan kan logga in direkt till Jamf Pro i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern sker görs IdP-initierad autentisering och auktorisering med enkel inloggning.

    c. Välj alternativet **NameID** för **USER MAPPING: SAML** (Användarmappning: SAML). Som standard är den här inställningen angiven till **NameID**, men du kan definiera ett anpassat attribut.

    d. Välj **E-post** för **USER MAPPING: JAMF PRO** (Användarmappning: JAMF PRO). Jamf Pro mappar SAML-attribut som skickas av IdP på följande sätt: via användare och via grupper. När en användare försöker ansluta till Jamf Pro hämtar Jamf Pro som standard information om användaren från identitetsprovidern och jämför den med Jamf Pro-användarkonton. Om det inkommande användarkontot inte finns i Jamf Pro sker matchning av gruppnamn.

    e. Klistra in värdet `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` i textrutan **GROUP ATTRIBUTE NAME** (Namn på gruppattribut).

7. På samma sida rullar du till **IDENTITY PROVIDER** (Identitetsprovider) i avsnittet **Enkel inloggning** och utför följande steg:

    ![Konfiguration av Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **andra** som ett alternativ från den **IDENTITETSPROVIDER** listrutan.

    b. I textrutan **OTHER PROVIDER** (Annan provider) anger du **Azure AD**.

    c. Välj **Metadata-URL** som ett alternativ från den **IDENTITETSKÄLLA PROVIDERN METADATA** listrutan och klistra in i textrutan följande den **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.

    d. Kopiera värdet för **entitets-ID** och klistra in det i textrutan **Identifierare (entitets-ID)** i avsnittet **Jamf Pro Domain and URLs** (Domän och URL:er för Jamf Pro) på Azure-portalen.

    > [!NOTE]
    > Här är det suddiga värdet underdomändelen. Använd det här värdet för att slutföra inloggnings-URL och svars-URL i avsnittet **Jamf Pro Domain and URLs** (Domän och URL:er för Jamf Pro) på Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Jamf Pro.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Jamf Pro**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Jamf Pro** i programlistan.

    ![Jamf Pro-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jamf-pro-test-user"></a>Skapa Jamf Pro-testanvändare

För att Azure AD-användare ska kunna logga in på Jamf Pro måste de etableras till Jamf Pro. För Jamf Pro är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din Jamf Pro-företagsplats som administratör.

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

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Jamf Pro-panelen i åtkomstpanelen bör du automatiskt loggas in på Jamf Pro som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
