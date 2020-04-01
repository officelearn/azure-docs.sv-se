---
title: 'Självstudiekurs: Azure Active Directory-integrering med Everbridge | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103261"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudiekurs: Azure Active Directory-integrering med Everbridge

I den här självstudien får du lära dig hur du integrerar Everbridge med Azure Active Directory (Azure AD).
När du integrerar Everbridge med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Everbridge.
* Tillåt att användarna automatiskt loggas in på Everbridge med sina Azure AD-konton. Den här åtkomstkontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure-portalen.
Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Everbridge behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Everbridge-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Everbridge stöder IDP-initierad SSO.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Lägg till Everbridge från Azure Marketplace

Om du vill konfigurera integreringen av Everbridge i Azure AD lägger du till Everbridge från Azure Marketplace i listan över hanterade SaaS-appar.

Så här lägger du till Everbridge från Azure Marketplace.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i det vänstra navigeringsfönstret .

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **in Everbridge**i sökrutan . Välj **Everbridge** på resultatpanelen och välj **Lägg till**.

     ![Everbridge i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Everbridge baserat på testanvändaren Britta Simon.
För enkel inloggning för att arbeta upprättar du en länkrelation mellan en Azure AD-användare och den relaterade användaren i Everbridge.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Everbridge slutför du följande byggblock:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera Everbridge som Everbridge manager portal enkel inloggning](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
- [Konfigurera Everbridge som Everbridge-medlemsportal enkel inloggning](#configure-everbridge-as-everbridge-member-portal-single-sign-on) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en Everbridge-testanvändare](#create-an-everbridge-test-user) för att ha en motsvarighet till Britta Simon i Everbridge som är kopplad till Azure AD-representationen för användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Everbridge.

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Everbridge-programintegration**. **Everbridge**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurera programmet antingen som managerportal *eller* som medlemsportal på både Azure-portalen och Everbridge-portalen.

4. Så här konfigurerar du **Everbridge-programmet** som **Everbridge-hanterare**i avsnittet **Grundläggande SAML-konfiguration:**

    ![Everbridge-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Ange en URL som följer mönstret i rutan **Identifierare**`https://sso.everbridge.net/<API_Name>`

    b. I rutan **Svars-URL** anger du en URL som följer mönstret`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med url-värdena för faktisk identifierare och svar. Kontakta [Everbridges supportteam](mailto:support@everbridge.com)för att få dessa värden. Du kan också referera till de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Så här konfigurerar du **Everbridge-programmet** som **Everbridge-medlemsportal**i avsnittet **Grundläggande SAML-konfiguration:**

  * Om du vill konfigurera programmet i IDP-initierat läge gör du så här:

     ![Everbridge-domän och webbadresser med enkel inloggningsinformation för IDP-initierat läge](common/idp-intiated.png)

    a. Ange en URL som följer mönstret i rutan **Identifierare**`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. I rutan **Svars-URL** anger du en URL som följer mönstret`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare webbadresser** och följer det här steget:

     ![Everbridge-domän och webbadresser med enkel inloggningsinformation för SP-initierat läge](common/both-signonurl.png)

     a. I rutan **Logga in på WEBBADRESS** anger du en WEBBADRESS som följer mönstret`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska värdena Identifierare, Svara på URL och Logga in på URL-värden. Kontakta [Everbridges supportteam](mailto:support@everbridge.com)för att få dessa värden. Du kan också referera till de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan Konfigurera enkel inloggning med SAML väljer du **Hämta** i avsnittet **SAML-signeringscertifikat** för att hämta **XML-koden för federationsmetadata**. **Set up Single Sign-On with SAML** Spara den på datorn.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera Everbridge** kopierar du de webbadresser du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggnings-URL
    - Azure AD-identifierare
    - Utloggnings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurera Everbridge som Everbridge manager portal enkel inloggning

Så här konfigurerar du SSO på **Everbridge** som ett **Everbridge manager-portalprogram.**
 
1. Logga in på Everbridge som administratör i ett annat webbläsarfönster.

1. Välj fliken **Inställningar** högst upp. Under **Säkerhet**väljer du **Enkel inloggning**.
   
     ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Ange namnet på identifieraren providern i rutan **Namn.** Ett exempel är ditt företagsnamn.
   
     b. Ange namnet på API:et i rutan **API-namn.**
   
     c. Välj **Välj fil** om du vill överföra metadatafilen som du hämtade från Azure-portalen.
   
     d. För **SAML-identitetsplats**väljer du **Identitet i elementet NameIdentifier i ämnesutdraget**.
   
     e. I rutan **Url för inloggningsadress för identitetsprovider** klistrar du in värdet **för inloggningsadressen** som du kopierade från Azure-portalen.
   
     f. För **Tjänsteprovider initierad begäranbindning**väljer du **HTTP-omdirigering**.

     g. Välj **Spara**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurera Everbridge som Everbridge-medlemsportal enkel inloggning

Om du vill konfigurera enkel inloggning på **Everbridge** som en **Everbridge-medlemsportal**skickar du den nedladdade **FEDERATION Metadata XML** till [Everbridge-supportteamet](mailto:support@everbridge.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Så här skapar du testanvändaren Britta Simon i Azure-portalen.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Länkar för användare och alla användare](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialogrutan **Användare** följer du dessa steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord.** Skriv ned värdet som visas i rutan **Lösenord.**

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Everbridge.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** >**Everbridge**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Everbridge**i programlistan .

    ![Everbridge länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länkar till användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Dialogrutan Lägg till tilldelning](common/add-assign-user.png)

5. Välj **Britta Simon** i användarlistan i dialogrutan **Användare och grupper.** Välj **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-everbridge-test-user"></a>Skapa en Everbridge-testanvändare

I det här avsnittet skapar du testanvändaren Britta Simon i Everbridge. Om du vill lägga till användare i Everbridge-plattformen arbetar du med [Everbridges supportteam](mailto:support@everbridge.com). Användare måste skapas och aktiveras i Everbridge innan du använder enkel inloggning. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa din azure AD-konfiguration med hjälp av åtkomstpanelen.

När du väljer panelen Everbridge på åtkomstpanelen ska du automatiskt loggas in på everbridge-kontot som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

