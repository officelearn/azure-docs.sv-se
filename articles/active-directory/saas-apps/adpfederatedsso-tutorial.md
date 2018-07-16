---
title: 'Självstudier: Azure Active Directory-integration med ADP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 75b84c2856373126ceba0fc536e41d270f4d2d05
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048787"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Självstudier: Azure Active Directory-integration med ADP

I den här självstudien får du lära dig hur du integrerar ADP med Azure Active Directory (AD Azure).

Integrera ADP med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ADP.
- Du kan aktivera användarna att automatiskt få loggat in på ADP (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ADP, behöver du följande objekt:

- En Azure AD-prenumeration
- En ADP aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ADP från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adp-from-the-gallery"></a>Att lägga till ADP från galleriet
För att konfigurera integrering av ADP i Azure AD, som du behöver lägga till ADP från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ADP från galleriet:**

1.  Logga in på din Microsoft Azure identity-providermiljö som administratör.

2. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

3. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
4. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

5. I sökrutan skriver **ADP**väljer **ADP** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP i resultatlistan](./media/adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ADP baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ADP är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ADP upprättas.

I ADP, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ADP, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ADP](#create-an-adp-test-user)**  – du har en motsvarighet för Britta Simon i ADP som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ADP-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ADP:**

1. I Azure-portalen på den **ADP** application integration klickar du på **fliken** och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange den **aktiverad för användare att logga in** fältet värde till **Ja**.

    b. Kopiera den **URL för användaråtkomst** och du klistra in det i **konfigurera inloggnings-URL-avsnittet**, som beskrivs senare i självstudien.

    c. Ange den **Användartilldelning krävs** fältet värde till **Ja**.

    d. Ange den **synlig för användarna** fältet värde till **nr**.

2. Klicka på **enkel inloggning** på **ADP** integrering programsidan.

    ![Konfigurera enkel inloggning för länken][4]

3. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. På den **ADP domän och URL: er** avsnittet, utför följande steg:

    ![ADP domän och URL: er med enkel inloggning för information](./media/adpfederatedsso-tutorial/tutorial_adp_url.png)

    I den **identifierare** textrutan anger du ett URL: `https://fed.adp.com` 
    
5. ADP programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Anspråkets namn kommer alltid att **”PersonImmutableID”** och värdet som vi har mappats till **employeeid**. 

    Här användarmappning från Azure AD till ADP kommer att utföras på den **employeeid** men kan du mappa till ett annat värde baserat på dina inställningar för programmet. Så du arbete med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) först att använda rätt identifierare för en användare och mappa värdet med den **”PersonImmutableID”** anspråk.

    ![Konfigurera enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | PersonImmutableID | User.EmployeeID |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-kontroll, måste du kontakta din [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att konfigurera anpassade anspråk för ditt program. 

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Att konfigurera enkel inloggning på **ADP** sida, måste du ladda upp den hämtade **XML-Metadata för** på den [ADP webbplats](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta några dagar. 

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP på nytt för federerad åtkomst

>[!Important]
> Dina anställda som behöver federerad tillgång till dina ADP tjänster måste tilldelas till ADP service-app och därefter, användare måste tilldelas till den specifika ADP-tjänsten.
Konfigurera ADP tjänster och tilldela/hantera användarna för att styra användarnas åtkomst till den specifika ADP-tjänsten vid mottagning av bekräftelse från din ADP-representant.

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ADP**väljer **ADP** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP i resultatlistan](./media/adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. I Azure-portalen på din **ADP** application integration klickar du på **fliken** och utför följande steg:  

    ![Linkedproperties för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange den **aktiverad för användare att logga in** fältet värde till **Ja**.

    b.  Ange den **Användartilldelning krävs** fältet värde till **Ja**.

    c.  Ange den **synlig för användarna** fältet värde till **Ja**.

6. Klicka på **enkel inloggning** på **ADP** integrering programsidan.

    ![Konfigurera enkel inloggning för länken][4]

7. På den **enkel inloggning** dialogrutan **läge** som **inloggning länkade**. länka ditt program till **ADP**.

    ![Enkel inloggning för länkad](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navigera till den **konfigurera inloggnings-URL** avsnittet, utför följande steg:

    ![Prop för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Klistra in den **URL för användaråtkomst**, som du har kopierat från ovan **fliken** (från den huvudsakliga ADP-app).
                                                             
    b. Följande är 5 appar som stöder olika **Relay tillstånd URL: er**. Du måste lägga till lämpliga **Relay tillstånd URL** värdet för ett visst program manuellt till den **URL för användaråtkomst**.
    
    * **ADP arbetsstyrka nu**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP arbetsstyrka nu förbättrats tid**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM-system**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Spara** dina ändringar.

10. Börja testa med en eller två användare vid mottagning av bekräftelse från din ADP-representant.

    a. Tilldela några användare till tjänsten ADP App för att testa federerad åtkomst.

    b. Testning är klar när användare kommer åt ADP service-app i galleriet och kan komma åt sina ADP-tjänsten.
 
11. Tilldela federerad ADP tjänsten till enskilda användare eller användargrupper som beskrivs senare i kursen och distribuera den till dina anställda vid bekräftelse av testet lyckats. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/adpfederatedsso-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/adpfederatedsso-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/adpfederatedsso-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/adpfederatedsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-adp-test-user"></a>Skapa en ADP testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ADP. Arbeta med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) att lägga till användare i ADP-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ADP.

![Tilldela rollen][200] 

**Om du vill tilldela ADP Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **ADP**.

    ![Länken ADP i listan med program](./media/adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ADP i åtkomstpanelen du bör få automatiskt loggat in på ditt ADP-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/adpfederatedsso-tutorial/tutorial_general_203.png

