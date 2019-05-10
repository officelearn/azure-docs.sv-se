---
title: 'Självstudier: Azure Active Directory-integrering med TOPdesk - offentliga | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk - offentliga.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: d5ecfcd249dd07dc94b3b17ea0a7a7de3559c681
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407944"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudier: Azure Active Directory-integrering med TOPdesk – offentlig

I den här självstudien får du lära dig hur du integrerar TOPdesk - offentliga med Azure Active Directory (AD Azure).
Integrera TOPdesk - offentliga med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TOPdesk - offentliga.
* Du kan aktivera användarna att vara automatiskt inloggad till TOPdesk - offentlig (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Konfigurera Azure AD-integrering med TOPdesk - offentlig, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - offentliga enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för TOPdesk - offentliga **SP** -initierad SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Att lägga till TOPdesk - offentliga från galleriet

Om du vill konfigurera integreringen av TOPdesk - offentliga till Azure AD som du behöver lägga till TOPdesk - offentliga från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TOPdesk - offentliga från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **TOPdesk - offentliga**väljer **TOPdesk - offentliga** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![TOPdesk - offentlig i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TOPdesk, offentlig baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning till arbete, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TOPdesk - måste offentliga upprättas.

Konfigurera och testa Azure AD enkel inloggning med TOPdesk - offentlig, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera TOPdesk - offentliga enkel inloggning](#configure-topdesk---public-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa TOPdesk - offentliga testanvändare](#create-topdesk---public-test-user)**  – du har en motsvarighet för Britta Simon i TOPdesk - offentlig som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera Azure AD enkel inloggning med TOPdesk - offentlig, utför följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **TOPdesk - offentliga** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4.  I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får den **tjänstleverantör metadatafil** från den **konfigurera TOPdesk - offentliga enkel inloggning** avsnitt som beskrivs senare i självstudien.

    a. Klicka på **Ladda upp metadatafil**.
    
    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts den **identifierare** och **svars-URL** värden får automatiskt ifylld i avsnittet grundläggande SAML-konfiguration.

    ![TOPdesk - offentlig domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    d. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net`

    e. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`

    > [!NOTE] 
    > Om den **identifierare** och **svars-URL** värden får inte fylls i automatiskt, måste du ange dem manuellt. Följer mönstret som nämns ovan för identifierare, och du får svars-URL-värde från den **konfigurera TOPdesk - offentliga enkel inloggning** avsnitt som beskrivs senare i självstudien. Den **inloggnings-URL** värdet är inte verkliga, så du behöver uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [TOPdesk - supportteamet för offentlig klient](https://help.topdesk.com/saas/enterprise/user/) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **konfigurera TOPdesk - offentliga** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurera TOPdesk - offentliga enkel inloggning

1. Logga in på din **TOPdesk - offentliga** företagets plats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.
   
    ![Inställningar](./media/topdesk-public-tutorial/ic790598.png "Inställningar")

3. Klicka på **Inloggningsinställningar**.
   
    ![Inloggningsinställningar](./media/topdesk-public-tutorial/ic790599.png "Inloggningsinställningar")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.
   
    ![Allmänt](./media/topdesk-public-tutorial/ic790600.png "Allmänt")

5. I den **offentliga** delen av den **SAML-inloggningen** konfiguration och utför följande steg:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/ic790601.png "Tekniska inställningar")
   
    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.
   
    b. Öppna den hämta metadatafilen och välj sedan den **AssertionConsumerService** noden.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiera den **AssertionConsumerService** värde, klistra in det här värdet i den **svars-URL** -textrutan i **SAML grundkonfiguration** avsnittet.      
   
6. Skapa en certifikatfil genom att utföra följande steg:
    
    ![Certifikat](./media/topdesk-public-tutorial/ic790606.png "Certifikat")
    
    a. Öppna den nedladdade metadatafilen från Azure-portalen.
    
    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.
    
    c. Kopiera värdet för noden **X509Certificate**.
    
    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.
    
    ![SAML-inloggning](./media/topdesk-public-tutorial/ic790625.png "SAML-inloggning")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:
    
    ![SAML-konfigurationsassistent](./media/topdesk-public-tutorial/ic790608.png "SAML-konfigurationsassistent")
    
    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    d. I textrutan **Användarnamnsattribut** skriver du `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TOPdesk - offentliga.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **TOPdesk - offentliga**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **TOPdesk - offentliga**.

    ![TOPdesk - offentlig länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-topdesk---public-test-user"></a>Skapa TOPdesk - offentliga testanvändare

För att aktivera Azure AD-användare att logga in på TOPdesk - offentlig, de måste etableras i TOPdesk - offentliga. När det gäller TOPdesk - offentliga etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din **TOPdesk - offentliga** företagets plats som administratör.

2. Klicka på menyn längst upp **TOPdesk \> New \> stödfiler \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. I dialogrutan Ny Person utför du följande steg:
   
    ![Ny Person](./media/topdesk-public-tutorial/ic790629.png "ny Person")
   
    a. Klicka på fliken Allmänt.

    b. I den **efternamn** textrutan skriver som Simon användarens efternamn
 
    c. Välj en **plats** för kontot.
 
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra TOPdesk - verktyg för att skapa offentliga användar-konto eller API: er som tillhandahålls av TOPdesk - publik för att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på TOPdesk - offentliga panelen i åtkomstpanelen, bör det vara loggas in automatiskt till TOPdesk - offentlig som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
