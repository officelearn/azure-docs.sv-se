---
title: 'Självstudier: Azure Active Directory-integration med TOPdesk - offentliga | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk - offentliga.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 7f8bedda36e4008bdb9c3aeefc4d5acdf6ba253e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042137"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudier: Azure Active Directory-integration med TOPdesk – offentlig

I den här självstudien får du lära dig hur du integrerar TOPdesk - offentliga med Azure Active Directory (AD Azure).

Integrera TOPdesk - offentliga med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TOPdesk - offentliga.
- Du kan aktivera användarna att automatiskt få loggat in på TOPdesk - offentlig (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med TOPdesk - offentlig, behöver du följande objekt:

- En Azure AD-prenumeration
- En TOPdesk - offentliga vid enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TOPdesk - offentliga från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-topdesk---public-from-the-gallery"></a>Att lägga till TOPdesk - offentliga från galleriet
Om du vill konfigurera integreringen av TOPdesk - offentliga till Azure AD som du behöver lägga till TOPdesk - offentliga från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TOPdesk - offentliga från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **TOPdesk - offentliga**väljer **TOPdesk - offentliga** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![TOPdesk - offentlig i resultatlistan](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TOPdesk – offentlig baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i TOPdesk – offentlig är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i TOPdesk - offentliga måste upprättas.

I TOPdesk - offentlig, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Konfigurera och testa Azure AD enkel inloggning med TOPdesk - offentlig, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en TOPdesk - offentliga testanvändare](#create-a-topdesk---public-test-user)**  – du har en motsvarighet för Britta Simon i TOPdesk - offentlig som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din TOPdesk - offentliga program.

**Konfigurera Azure AD enkel inloggning med TOPdesk - offentlig, utför följande steg:**

1. I Azure-portalen på den **TOPdesk - offentliga** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. På den **TOPdesk - URL: er och den offentliga domänen** avsnittet, utför följande steg:

    ![TOPdesk - offentlig domän och URL: er med enkel inloggning för information](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net`
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Svars-URL är explaned senare i självstudien. Kontakta [TOPdesk - supportteamet för offentlig klient](https://help.topdesk.com/saas/enterprise/user/) att hämta dessa värden.  

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
6. På den **TOPdesk - offentliga konfiguration** klickar du på **konfigurera TOPdesk - offentliga** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TOPdesk - offentliga konfiguration](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Logga in på din **TOPdesk - offentliga** företagets plats som administratör.

8. I den **TOPdesk** -menyn klickar du på **inställningar**.
   
    ![Inställningar för](./media/topdesk-public-tutorial/ic790598.png "inställningar")

9. Klicka på **inloggningsinställningar**.
   
    ![Inloggningsinställningar](./media/topdesk-public-tutorial/ic790599.png "inloggningsinställningar")

10. Expandera den **inloggningsinställningar** menyn och klicka sedan på **Allmänt**.
   
    ![Allmän](./media/topdesk-public-tutorial/ic790600.png "Allmänt")

11. I den **offentliga** delen av den **SAML-inloggningen** konfiguration och utför följande steg:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/ic790601.png "tekniska inställningar")
   
    a. Klicka på **hämta** att hämta offentliga metadatafilen och spara den lokalt på datorn.
   
    b. Öppna den hämta metadatafilen och välj sedan den **AssertionConsumerService** noden.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiera den **AssertionConsumerService** värde, klistra in det här värdet i den **svars-URL** -textrutan i **TOPdesk - URL: er och den offentliga domänen** avsnittet.      
   
12. Utför följande steg för att skapa en certifikatfil:
    
    ![Certifikatet](./media/topdesk-public-tutorial/ic790606.png "certifikat")
    
    a. Öppna metadatafilen hämtade från Azure-portalen.
    
    b. Expandera den **RoleDescriptor** nod som har en **xsi: type** av **aggregeringsdesignprocessen: ApplicationServiceType**.
    
    c. Kopiera värdet för den **X509Certificate** noden.
    
    d. Spara den kopierade **X509Certificate** värdet lokalt på datorn i en fil.

13. I den **offentliga** klickar du på **Lägg till**.
    
    ![SAML-inloggningen](./media/topdesk-public-tutorial/ic790625.png "SAML-inloggning")

14. På den **SAML configuration assistent** dialogrutan utför följande steg:
    
    ![SAML-konfiguration assistent](./media/topdesk-public-tutorial/ic790608.png "assistent för SAML-konfiguration")
    
    a. Ladda upp din hämtade metadatafilen från Azure-portalen under **Federationsmetadata**, klickar du på **Bläddra**.

    b. Ladda upp din certifikatfil under **certifikat (RSA)**, klickar du på **Bläddra**.

    c. Ladda upp logotypfilen som du fick från supporten TOPdesk under **logotyp ikonen**, klickar du på **Bläddra**.

    d. I den **användarnamnsattributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I den **visningsnamn** textrutan anger du ett namn för din konfiguration.

    f. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/topdesk-public-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/topdesk-public-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/topdesk-public-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-topdesk---public-test-user"></a>Skapa en TOPdesk - offentliga testanvändare

För att aktivera Azure AD-användare att logga in på TOPdesk - offentlig, de måste etableras i TOPdesk - offentliga.  
När det gäller TOPdesk - offentliga etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TOPdesk - offentliga.

![Tilldela rollen][200] 

**Tilldela Britta Simon till TOPdesk - offentlig, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **TOPdesk - offentliga**.

    ![TOPdesk - offentlig länk i listan med program](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
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

