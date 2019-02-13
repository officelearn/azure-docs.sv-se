---
title: 'Självstudier: Azure Active Directory-integrering med TOPdesk - offentliga | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk - offentliga.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89412040fdea32746574d8ae5bada9c017617b80
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185000"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudier: Azure Active Directory-integrering med TOPdesk – offentlig

I den här självstudien får du lära dig hur du integrerar TOPdesk - offentliga med Azure Active Directory (AD Azure).

Integrera TOPdesk - offentliga med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TOPdesk - offentliga.
- Du kan aktivera användarna att automatiskt få loggat in på TOPdesk - offentlig (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med TOPdesk - offentlig, behöver du följande objekt:

- En Azure AD-prenumeration
- En TOPdesk - offentliga vid enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TOPdesk - offentliga från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-topdesk---public-from-the-gallery"></a>Att lägga till TOPdesk - offentliga från galleriet
Om du vill konfigurera integreringen av TOPdesk - offentliga till Azure AD som du behöver lägga till TOPdesk - offentliga från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TOPdesk - offentliga från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **TOPdesk - offentliga**väljer **TOPdesk - offentliga** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![TOPdesk - offentlig i resultatlistan](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TOPdesk – offentlig baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i TOPdesk – offentlig är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i TOPdesk - offentliga måste upprättas.

I TOPdesk - offentlig, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Konfigurera och testa Azure AD enkel inloggning med TOPdesk - offentlig, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en TOPdesk - offentliga testanvändare](#create-a-topdesk---public-test-user)**  – du har en motsvarighet för Britta Simon i TOPdesk - offentlig som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din TOPdesk - offentliga program.

**Konfigurera Azure AD enkel inloggning med TOPdesk - offentlig, utför följande steg:**

1. I Azure-portalen på den **TOPdesk - offentliga** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. På den **TOPdesk - URL: er och den offentliga domänen** avsnittet, utför följande steg:

    ![TOPdesk - offentlig domän och URL: er med enkel inloggning för information](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.topdesk.net`
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Svars-URL är explaned senare i självstudien. Kontakta [TOPdesk - supportteamet för offentlig klient](https://help.topdesk.com/saas/enterprise/user/) att hämta dessa värden.  

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. På den **TOPdesk - offentliga konfiguration** klickar du på **konfigurera TOPdesk - offentliga** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TOPdesk - offentliga konfiguration](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Logga in på din **TOPdesk - offentliga** företagets plats som administratör.

1. På menyn **TOPdesk** klickar du på **Inställningar**.
   
    ![Inställningar](./media/topdesk-public-tutorial/ic790598.png "Inställningar")

1. Klicka på **Inloggningsinställningar**.
   
    ![Inloggningsinställningar](./media/topdesk-public-tutorial/ic790599.png "Inloggningsinställningar")

1. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.
   
    ![Allmänt](./media/topdesk-public-tutorial/ic790600.png "Allmänt")

1. I den **offentliga** delen av den **SAML-inloggningen** konfiguration och utför följande steg:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/ic790601.png "Tekniska inställningar")
   
    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.
   
    b. Öppna den hämta metadatafilen och välj sedan den **AssertionConsumerService** noden.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiera den **AssertionConsumerService** värde, klistra in det här värdet i den **svars-URL** -textrutan i **TOPdesk - URL: er och den offentliga domänen** avsnittet.      
   
1. Skapa en certifikatfil genom att utföra följande steg:
    
    ![Certifikat](./media/topdesk-public-tutorial/ic790606.png "Certifikat")
    
    a. Öppna den nedladdade metadatafilen från Azure-portalen.
    
    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.
    
    c. Kopiera värdet för noden **X509Certificate**.
    
    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

1. I avsnittet **Offentligt** klickar du på **Lägg till**.
    
    ![SAML-inloggningen](./media/topdesk-public-tutorial/ic790625.png "SAML-inloggning")

1. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:
    
    ![SAML-konfigurationsassistent](./media/topdesk-public-tutorial/ic790608.png "SAML-konfigurationsassistent")
    
    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    d. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    f. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-topdesk---public-test-user"></a>Skapa en TOPdesk - offentliga testanvändare

För att aktivera Azure AD-användare att logga in på TOPdesk - offentlig, de måste etableras i TOPdesk - offentliga.  
När det gäller TOPdesk - offentliga etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:
1. Logga in på din **TOPdesk - offentliga** företagets plats som administratör.

1. Klicka på menyn längst upp **TOPdesk \> New \> stödfiler \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

1. I dialogrutan Ny Person utför du följande steg:
   
    ![Ny Person](./media/topdesk-public-tutorial/ic790629.png "ny Person")
   
    a. Klicka på fliken Allmänt.

    b. I den **efternamn** textrutan skriver som Simon användarens efternamn
 
    c. Välj en **plats** för kontot.
 
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra TOPdesk - verktyg för att skapa offentliga användar-konto eller API: er som tillhandahålls av TOPdesk - publik för att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TOPdesk - offentliga.

![Tilldela rollen][200] 

**Tilldela Britta Simon till TOPdesk - offentlig, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **TOPdesk - offentliga**.

    ![TOPdesk - offentlig länk i listan med program](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på TOPdesk - offentliga panelen i åtkomstpanelen, du bör få automatiskt loggat in på ditt TOPdesk - offentliga program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

