---
title: 'Självstudier: Azure Active Directory-integrering med EverBridge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EverBridge.
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
ms.openlocfilehash: 886cfc59ed41e25c8c3953888690f58e4cc4c252
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695340"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudier: Azure Active Directory-integrering med EverBridge

I den här självstudien får du lära dig hur du integrerar EverBridge med Azure Active Directory (AD Azure).
Integrera EverBridge med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till EverBridge.
* Du kan aktivera användarna att vara automatiskt inloggad till EverBridge (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med EverBridge, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* EverBridge enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för EverBridge **IDP** -initierad SSO

## <a name="adding-everbridge-from-the-gallery"></a>Att lägga till EverBridge från galleriet

För att konfigurera integrering av EverBridge i Azure AD, som du behöver lägga till EverBridge från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EverBridge från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **EverBridge**väljer **EverBridge** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![EverBridge i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med EverBridge baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i EverBridge upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med EverBridge, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera EverBridge som EverBridge Manager Portal enkel inloggning](#configure-everbridge-as-everbridge-manager-portal-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Konfigurera EverBridge som EverBridge Manager Portal enkel inloggning](#configure-everbridge-as-everbridge-member-portal-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
4. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Skapa testanvändare EverBridge](#create-everbridge-test-user)**  – du har en motsvarighet för Britta Simon i EverBridge som är länkad till en Azure AD-representation av användaren.
7. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med EverBridge:

1. I den [Azure-portalen](https://portal.azure.com/)på den **EverBridge** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

    >[!NOTE]
    >Du behöver göra konfigurationer av programmet som Manager-portalen eller som medlem portalen i bägge ändar dvs. på Azure Portal och Everbridge-portalen.

4. Så här konfigurerar du den **EverBridge** programmet som **EverBridge Manager-portalen**på den **SAML grundkonfiguration** avsnittet utför följande steg:

    ![EverBridge domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://sso.everbridge.net/<API_Name>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [EverBridge supportteamet](mailto:support@everbridge.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Så här konfigurerar du den **EverBridge** programmet som **EverBridge medlem Portal**på den **grundkonfiguration SAML** avsnittet, utför följande steg:

   * Om du vill konfigurera programmet i **IDP** initierade läge:

    ![EverBridge domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    * I textrutan **Identifierare** anger du en URL med följande mönster: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    * I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

* Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![EverBridge domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    * I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och URL: en inloggning. Kontakta [EverBridge supportteamet](mailto:support@everbridge.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **konfigurera EverBridge** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurera EverBridge som EverBridge Manager Portal Single Sign-On

1. Att hämta SSO konfigurerats för **EverBridge** som **EverBridge Manager-portalen** program, utför följande steg: 
 
2. I ett annat webbläsarfönster, loggar du in EverBridge som administratör.

3. Klicka på menyn längst upp i **inställningar** fliken och markera **enkel inloggning** under **Security**.
   
     ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. I den **namn** textrutan skriver du namnet på ID-providern (till exempel: namnet på ditt företag).
   
     b. I den **API-namn** textrutan skriver du namnet på API: et.
   
     c. Klicka på **Välj fil** knappen för att ladda upp metadatafilen som du har hämtat från Azure-portalen.
   
     d. I SAML identitet plats, väljer **identitet är i NameIdentifier-elementet i instruktionen ämne**.
   
     e. I den **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.
   
     f. I den Provider initierade begära Tjänstbindning, väljer **omdirigering för HTTP**.

     g. Klicka på **Spara**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurera EverBridge som EverBridge medlem Portal enkel inloggning

Att konfigurera enkel inloggning på **EverBridge** som **EverBridge medlem Portal**, du behöver skicka de hämtade **XML-Metadata för Federation** till [Everbridge supportteam](mailto:support@everbridge.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EverBridge.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **EverBridge**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **EverBridge**.

    ![Länken EverBridge i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-everbridge-test-user"></a>Skapa EverBridge testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Everbridge. Arbeta med [EverBridge supportteamet](mailto:support@everbridge.com) att lägga till användare i Everbridge-plattformen. Användare måste skapas och aktiveras i EverBridge innan du använder enkel inloggning. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen EverBridge i åtkomstpanelen, bör det vara loggas in automatiskt till EverBridge som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

