---
title: "Självstudier: Azure Active Directory-integrering med ADP federerad enkel inloggning | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP federerad enkel inloggning."
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
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: d2049e3bc8a35792703d06374263466ddc35b5ce
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>Självstudier: Azure Active Directory-integrering med ADP federerad enkel inloggning

I kursen får lära du att integrera ADP federerad enkel inloggning med Azure Active Directory (AD Azure).

Integrera ADP federerad enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till ADP federerad enkel inloggning.
- Du kan aktivera användarna att automatiskt hämta loggat in på ADP federerad enkel inloggning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ADP federerad enkel inloggning, behöver du följande:

- En Azure AD-prenumeration
- En prenumeration på ADP federerad enkel inloggning aktiverad

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ADP federerad enkel inloggning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adp-federated-sso-from-the-gallery"></a>Att lägga till ADP federerad enkel inloggning från galleriet
Du måste lägga till ADP federerad enkel inloggning från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ADP federerad enkel inloggning i Azure AD.

**Utför följande steg för att lägga till ADP federerad enkel inloggning från galleriet:**

1.  Logga in på Microsoft Azure identitet providern miljön som administratör.

2. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

3. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
4. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

5. I sökrutan skriver **ADP federerad enkel inloggning**väljer **ADP federerad enkel inloggning** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP federerad enkel inloggning i resultatlistan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ADP federerad enkel inloggning baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i ADP federerad enkel inloggning till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ADP federerad enkel inloggning upprättas.

I ADP federerad enkel inloggning, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ADP federerad enkel inloggning måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ADP federerad enkel inloggning](#create-an-adp-federated-sso-test-user)**  – du har en motsvarighet för Britta Simon i ADP federerad enkel inloggning som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet ADP federerad enkel inloggning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ADP federerad enkel inloggning:**

1. I Azure-portalen på den **ADP federerad enkel inloggning** programmet integration klickar du på **fliken Egenskaper** och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a. Ange den **aktiverats för användare att logga in** fältvärde till **Ja**.

    b. Kopiera den **användaren åtkomst-URL** och du behöver klistra in den i **konfigurera inloggnings-URL-avsnittet**, som beskrivs senare i självstudierna.

    c. Ange den **Användartilldelning krävs** fältvärde till **Ja**.

    d. Ange den **synlig för användarna** fältvärde till **nr**.

2. Klicka på **enkel inloggning** på **ADP federerad enkel inloggning** sidan för integrering av programmet.

    ![Konfigurera enkel inloggning länk][4]

3. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. På den **ADP federerad enkel inloggning domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och ADP federerad enkel inloggning domän med enkel inloggning information](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    I den **identifierare** textruta, ange ett URL-Adressen:`https://fed.adp.com/` 
    
5. Programmet ADP federerad enkel inloggning förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. Anspråkets namn kommer alltid att **”PersonImmutableID”** och värdet som vi har mappats till **employeeid**. 

    Här mappning från Azure AD till ADP federerad enkel inloggning ska ske på den **employeeid** men du kan mappa till ett annat värde baserat på dina inställningar för programmet. Så kan du arbeta med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) först att använda rätt ID för en användare och mappa värdet med den **”PersonImmutableID”** anspråk.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

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

    ![Länken hämta certifikatet](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. Konfigurera enkel inloggning på **ADP federerad enkel inloggning** sida, måste du ladda upp den hämtade **XML-Metadata för** på den [ADP federerad enkel inloggning webbplats](https://adpfedsso.adp.com/public/login/index.fcc).

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

4. I sökrutan skriver **ADP federerad enkel inloggning**väljer **ADP federerad enkel inloggning** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ADP federerad enkel inloggning i resultatlistan](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. I Azure-portalen på din **ADP federerad enkel inloggning** programmet integration klickar du på **fliken Egenskaper** och utför följande steg:  

    ![Linkedproperties för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.  Ange den **aktiverats för användare att logga in** fältvärde till **Ja**.

    b.  Ange den **Användartilldelning krävs** fältvärde till **Ja**.

    c.  Ange den **synlig för användarna** fältvärde till **Ja**.

6. Klicka på **enkel inloggning** på **ADP federerad enkel inloggning** sidan för integrering av programmet.

    ![Konfigurera enkel inloggning länk][4]

7. På den **enkel inloggning** markerar **läge** som **inloggning länkade**. länka ditt program till **ADP federerad enkel inloggning**.

    ![Enkel inloggning länkade](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. Navigera till den **konfigurera inloggnings-URL** avsnittet, utför följande steg:

    ![Prop för enkel inloggning](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a. Klistra in den **användaren åtkomst-URL**, som du har kopierat från ovan **fliken Egenskaper** (från den huvudsakliga ADP federerad enkel inloggning app).
                                                             
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

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

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
 
### <a name="create-an-adp-federated-sso-test-user"></a>Skapa en testanvändare ADP federerad enkel inloggning

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i ADP federerad enkel inloggning. Arbeta med [ADP supportteamet](https://www.adp.com/contact-us/overview.aspx) att lägga till användare i kontot ADP federerad enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ADP federerad enkel inloggning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ADP federerad enkel inloggning, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ADP federerad enkel inloggning**.

    ![Länken ADP federerad enkel inloggning i listan med program](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ADP federerad enkel inloggning på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet ADP federerad enkel inloggning.
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

