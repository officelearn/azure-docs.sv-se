---
title: 'Självstudiekurs: Azure Active Directory-integrering med TOPdesk – Offentlig | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk - Offentlig.
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
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71950410"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudiekurs: Azure Active Directory-integrering med TOPdesk - Offentlig

I den här självstudien får du lära dig hur du integrerar TOPdesk - Offentlig med Azure Active Directory (Azure AD).
Integrering av TOPdesk – Offentlig med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TOPdesk - Offentlig.
* Du kan aktivera dina användare så att de automatiskt loggas in på TOPdesk – Offentlig (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TOPdesk - Offentlig behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - Offentlig prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TOPdesk - Allmänheten stöder **SP** initierade SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Lägga till TOPdesk - Offentligt från galleriet

Om du vill konfigurera integreringen av TOPdesk – Offentlig i Azure AD måste du lägga till TOPdesk - Offentlig från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till TOPdesk - Offentlig från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **TOPdesk - Offentlig**i sökrutan och välj **TOPdesk - Offentlig** från resultatpanelen och klicka sedan på **Lägg** till knappen För att lägga till programmet.

     ![TOPdesk - Offentligt i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TOPdesk - Offentlig baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TOPdesk - Offentlig upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TOPdesk – Offentlig måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TOPdesk - Offentlig enkel inloggning](#configure-topdesk---public-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TOPdesk - Offentlig testanvändare](#create-topdesk---public-test-user)** - för att ha en motsvarighet till Britta Simon i TOPdesk - Offentlig som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med TOPdesk - Offentlig:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **TOPdesk - Offentlig** programintegrering . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4.  I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får **metadatafilen för Tjänsteleverantören** från avsnittet **Konfigurera TOPdesk - Offentlig enkel inloggning** som förklaras senare i självstudien.

    a. Klicka på **Ladda upp metadatafil**.
    
    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts fylls **url-värdena identifierare** och **svar** i automatiskt i avsnittet Grundläggande SAML-konfiguration.

    ![TOPdesk - Allmän domän och webbadresser enkel inloggningsinformation](common/sp-identifier-reply.png)

    d. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<companyname>.topdesk.net`

    e. I textrutan **Identifierare URL** fyller du i url:en för TOPdesk-metadata som du kan hämta från topdesk-konfigurationen. Den bör använda följande mönster:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Om **URL-värdena Identifierare** och **Svara** inte fylls i automatiskt måste du ange dem manuellt. För Identifierare följer du mönstret som nämnts ovan och du får svars-URL-värde från avsnittet **Konfigurera TOPdesk - Offentlig enkel inloggning** som förklaras senare i självstudien. Värdet **för inloggnings-URL** är inte verkligt, så du måste uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [TOPdesk - Supportteamet](https://help.topdesk.com/saas/enterprise/user/) för offentliga klienter för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera TOPdesk - Offentlig.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurera TOPdesk - Offentlig enkel inloggning

1. Logga in på **din TOPdesk - Offentliga** företag webbplats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.
   
    ![Inställningar](./media/topdesk-public-tutorial/ic790598.png "Inställningar")

3. Klicka på **Inloggningsinställningar**.
   
    ![Inställningar för inloggning](./media/topdesk-public-tutorial/ic790599.png "Inställningar för inloggning")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.
   
    ![Allmänt](./media/topdesk-public-tutorial/ic790600.png "Allmänt")

5. I avsnittet **Offentlig** i avsnittet **SAML-inloggningskonfiguration** gör du följande:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/ic790601.png "Tekniska inställningar")
   
    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.
   
    b. Öppna den hämtade metadatafilen och leta sedan reda på noden **AssertionConsumerService.**

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiera **värdet AssertionConsumerService** och klistra in det här värdet i textrutan **Svara URL** i avsnittet **Grundläggande SAML-konfiguration.**      
   
6. Skapa en certifikatfil genom att utföra följande steg:
    
    ![Certifikat](./media/topdesk-public-tutorial/ic790606.png "Certifikat")
    
    a. Öppna den nedladdade metadatafilen från Azure-portalen.
    
    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.
    
    c. Kopiera värdet för noden **X509Certificate**.
    
    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.
    
    ![SAML-inloggning](./media/topdesk-public-tutorial/ic790625.png "SAML-inloggning")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:
    
    ![KONFIGURATIONSassistent för SAML](./media/topdesk-public-tutorial/ic790608.png "KONFIGURATIONSassistent för SAML")
    
    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    d. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till TOPdesk - Offentlig.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **TOPdesk - Offentlig**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **TOPdesk - Offentlig**i programlistan .

    ![Den TOPdesk - Offentlig länk i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-topdesk---public-test-user"></a>Skapa TOPdesk - Offentlig testanvändare

För att Azure AD-användare ska kunna logga in på TOPdesk - Offentlig måste de etableras i TOPdesk - Offentlig. När det gäller TOPdesk - Offentlig är etablering en manuell uppgift.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på **din TOPdesk - Offentliga** företagets webbplats som administratör.

2. Klicka på **TOPdesk \> \> Ny supportfil \> person**på menyn högst upp .
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. Gör följande i dialogrutan Ny person:
   
    ![Ny person](./media/topdesk-public-tutorial/ic790629.png "Ny person")
   
    a. Klicka på fliken Allmänt.

    b. Skriv Efternamnet för användaren som Simon i textrutan **Efternamn**
 
    c. Välj en **webbplats** för kontot.
 
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra TOPdesk - Verktyg för att skapa offentliga användarkonton eller API:er som tillhandahålls av TOPdesk - Offentliga för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TOPdesk - Offentlig på åtkomstpanelen ska du automatiskt loggas in på den TOPdesk - Offentlig som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
