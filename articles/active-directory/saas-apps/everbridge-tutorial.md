---
title: 'Självstudier: Azure Active Directory-integrering med Everbridge | Microsoft Docs'
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
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231462"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudier: Azure Active Directory-integrering med Everbridge

I den här självstudien får du lära dig hur du integrerar Everbridge med Azure Active Directory (AD Azure).
När du integrerar Everbridge med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Everbridge.
* Tillåt användarna att logga in automatiskt till Everbridge med sina Azure AD-konton. Den här åtkomstkontroll kallas för enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure portal.
Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Everbridge, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Everbridge-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Everbridge stöder IDP-initierad SSO.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Lägg till Everbridge från Azure Marketplace

Lägg till Everbridge från Azure Marketplace till din lista över hanterade SaaS-appar för att konfigurera integrering av Everbridge i Azure AD.

Följ dessa steg för att lägga till Everbridge från Azure Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret väljer **Azure Active Directory**.

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Everbridge**. Välj **Everbridge** i resultatet panelen och välj **Lägg till**.

     ![Everbridge i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Everbridge baserat på testanvändare Britta Simon.
För enkel inloggning att fungera, skapar du en länk-relation mellan en Azure AD-användare och relaterade användaren i Everbridge.

Om du vill konfigurera och testa Azure AD enkel inloggning med Everbridge, utför du följande byggblock:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera Everbridge som Everbridge manager portal enkel inloggning](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
- [Konfigurera Everbridge som Everbridge medlem portal enkel inloggning](#configure-everbridge-as-everbridge-member-portal-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en testanvändare Everbridge](#create-an-everbridge-test-user) har en motsvarighet för Britta Simon i Everbridge som är länkad till en Azure AD-representation av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg om du vill konfigurera Azure AD enkel inloggning med Everbridge.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Everbridge** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer **redigera** att öppna den **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurera programmet antingen som manager-portalen *eller* som portal medlem i både Azure-portalen och Everbridge-portal.

4. Så här konfigurerar du den **Everbridge** programmet som den **Everbridge manager-portalen**i den **grundkonfiguration SAML** , följer du dessa steg:

    ![Everbridge domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I den **identifierare** anger en URL som följer mönstret `https://sso.everbridge.net/<API_Name>`

    b. I den **svars-URL** anger en URL som följer mönstret `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska värdena för identifierare och svars-URL. För att få dessa värden kan kontakta den [Everbridge supportteamet](mailto:support@everbridge.com). Du kan också gå till de mönster som visas i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

5. Så här konfigurerar du den **Everbridge** programmet som den **Everbridge medlem portal**i den **grundläggande SAML-konfiguration** , följer du dessa steg:

  * Följ dessa steg om du vill konfigurera programmet i IDP-initierad läge:

     ![Everbridge domän och URL: er med enkel inloggning information för IDP-initierad läge](common/idp-intiated.png)

    a. I den **identifierare** anger en URL som följer mönstret `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. I den **svars-URL** anger en URL som följer mönstret `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser** och följer du detta steg:

     ![Everbridge domän och URL: er med enkel inloggning för information om SP-initierat läge](common/both-signonurl.png)

     a. I den **inloggnings-URL** anger en URL som följer mönstret `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL och logga in URL-värden. För att få dessa värden kan kontakta den [Everbridge supportteamet](mailto:support@everbridge.com). Du kan också gå till de mönster som visas i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **Federation Metadata-XML** . Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

7. I den **konfigurera Everbridge** avsnittet, Kopiera URL: er som du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggningswebbadress
    - Microsoft Azure Active Directory-identifierare
    - Utloggnings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurera Everbridge som Everbridge manager portal enkel inloggning

Att konfigurera enkel inloggning på **Everbridge** som en **Everbridge manager-portalen** program, Följ stegen nedan.
 
1. I ett annat webbläsarfönster, loggar du in Everbridge som administratör.

1. I menyn högst upp väljer du den **inställningar** fliken. Under **Security**väljer **enkel inloggning**.
   
     ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. I den **namn** anger du namnet på ID-provider. Ett exempel är namnet på ditt företag.
   
     b. I den **API-namn** anger du namnet på API: et.
   
     c. Välj **Välj fil** att ladda upp metadatafilen som du laddade ned från Azure-portalen.
   
     d. För **SAML identitet plats**väljer **identitet är i NameIdentifier-elementet i instruktionen ämne**.
   
     e. I den **inloggnings-URL för identitetsprovider** rutan, klistra in den **inloggnings-URL** värde som du kopierade från Azure-portalen.
   
     f. För **tjänstleverantör initierade begära bindning**väljer **omdirigering för HTTP**.

     g. Välj **Spara**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurera Everbridge som Everbridge medlem portal enkel inloggning

Att konfigurera enkel inloggning på **Everbridge** som en **Everbridge medlem portal**, skicka de hämtade **XML-Metadata för Federation** till den [Everbridge supportteam](mailto:support@everbridge.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Följ dessa steg om du vill skapa testanvändare Britta Simon i Azure-portalen.

1. I Azure-portalen går du till den vänstra panelen och väljer **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användare och alla användare länkar](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialogrutan **Användare** följer du dessa steg.

    ![Användardialogrutan](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** anger `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Välj den **visa lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

Aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Everbridge.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** >**Everbridge**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Everbridge**.

    ![Everbridge länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länk för användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Tilldelning av dialogrutan Lägg till](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan. Välj **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-everbridge-test-user"></a>Skapa en Everbridge testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Everbridge. Om du vill lägga till användare i Everbridge-plattformen, arbeta med den [Everbridge supportteamet](mailto:support@everbridge.com). Användare måste skapas och aktiveras i Everbridge innan du använder enkel inloggning. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Everbridge i åtkomstpanelen, bör det vara loggas in automatiskt till Everbridge kontot som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

