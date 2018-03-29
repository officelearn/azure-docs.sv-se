---
title: 'Självstudier: Azure Active Directory-integrering med ADP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeedes
ms.openlocfilehash: 038b2337ea49b769c6b19e9d50a5f0f2edd13d56
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Självstudier: Azure Active Directory-integrering med ADP

I kursen får lära du att integrera ADP med Azure Active Directory (AD Azure).

Integrera ADP med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till ADP.
- Du kan aktivera användarna att automatiskt hämta loggat in på ADP (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ADP behöver du följande:

- En Azure AD-prenumeration
- En prenumeration på ADP aktiverad

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ADP från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adp-from-the-gallery"></a>Att lägga till ADP från galleriet
Du måste lägga till ADP från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ADP i Azure AD.

**Utför följande steg för att lägga till ADP från galleriet:**

1.  Logga in på Microsoft Azure identitet providern miljön som administratör.

2. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

3. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
4. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

5. I sökrutan skriver **ADP**väljer **ADP** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP i resultatlistan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ADP baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ADP motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ADP upprättas.

I ADP, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ADP, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ADP](#create-an-adp-test-user)**  – du har en motsvarighet för Britta Simon i ADP som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ADP-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ADP:**

1. I Azure-portalen på den **ADP** programmet integration klickar du på **fliken Egenskaper** och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange den **aktiverats för användare att logga in** fältvärde till **Ja**.

    b. Kopiera den **användaren åtkomst-URL** och du behöver klistra in den i **konfigurera inloggnings-URL-avsnittet**, som beskrivs senare i självstudierna.

    c. Ange den **Användartilldelning krävs** fältvärde till **Ja**.

    d. Ange den **synlig för användarna** fältvärde till **nr**.

2. Klicka på **enkel inloggning** på **ADP** sidan för integrering av programmet.

    ![Konfigurera enkel inloggning länk][4]

3. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. På den **ADP domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och ADP domän med enkel inloggning information](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_url.png)

    I den **identifierare** textruta, ange ett URL-Adressen: `https://fed.adp.com` 
    
5. Programmet ADP förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. Anspråkets namn kommer alltid att **”PersonImmutableID”** och värdet som vi har mappats till **employeeid**. 

    Här mappning från Azure AD till ADP kommer att utföras på den **employeeid** men du kan mappa till ett annat värde baserat på dina inställningar för programmet. Så kan du arbeta med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) först att använda rätt ID för en användare och mappa värdet med den **”PersonImmutableID”** anspråk.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | PersonImmutableID | User.EmployeeID |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-kontroll måste du kontakta din [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program. 

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Konfigurera enkel inloggning på **ADP** sida, måste du ladda upp den hämtade **XML-Metadata för** på den [ADP webbplats](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta ett par dagar. 

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP tjänster för federerad åtkomst

>[!Important]
> Dina anställda som behöver federerad åtkomst till dina ADP-tjänster måste vara tilldelade till ADP service-appen och därefter, användare måste tilldelas specifika ADP-tjänsten.
Konfigurera ADP tjänster och tilldela/hantera användare att styra användarnas åtkomst till specifika ADP tjänsten vid mottagning av bekräftelse från ADP-representant.

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ADP**väljer **ADP** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP i resultatlistan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. I Azure-portalen på din **ADP** programmet integration klickar du på **fliken Egenskaper** och utför följande steg:  

    ![Linkedproperties för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange den **aktiverats för användare att logga in** fältvärde till **Ja**.

    b.  Ange den **Användartilldelning krävs** fältvärde till **Ja**.

    c.  Ange den **synlig för användarna** fältvärde till **Ja**.

6. Klicka på **enkel inloggning** på **ADP** sidan för integrering av programmet.

    ![Konfigurera enkel inloggning länk][4]

7. På den **enkel inloggning** markerar **läge** som **inloggning länkade**. länka ditt program till **ADP**.

    ![Enkel inloggning länkade](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navigera till den **konfigurera inloggnings-URL** avsnittet, utför följande steg:

    ![Prop för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Klistra in den **användaren åtkomst-URL**, som du har kopierat från ovan **fliken Egenskaper** (från den huvudsakliga ADP-app).
                                                             
    b. Följande är 5 appar som stöder olika **Relay tillstånd URL: er**. Du måste lägga till lämpliga **Relay tillstånd URL** värdet för ett visst program manuellt till den **användaren åtkomst-URL**.
    
    * **ADP arbetsstyrka nu**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP arbetsstyrka nu förbättrats tid**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Spara** ändringarna.

10. Börja testa med en eller två användare vid mottagning av bekräftelse från ADP-representant.

    a. Tilldela några användare till tjänsten ADP App att testa federerad åtkomst.

    b. Testet lyckas när användare kommer åt ADP service-appen på galleriet och kan komma åt sina ADP-tjänsten.
 
11. Tilldela federerade ADP-tjänsten för enskilda användare eller användargrupper som beskrivs senare i guiden och distribuera den till dina anställda vid bekräftelse av lyckade testet. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-adp-test-user"></a>Skapa en ADP testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i ADP. Arbeta med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) att lägga till användare i ADP-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ADP.

![Tilldela rollen][200] 

**Om du vill tilldela ADP Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ADP**.

    ![Länken ADP i listan med program](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ADP på åtkomstpanelen du bör få automatiskt loggat in på ditt ADP-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

