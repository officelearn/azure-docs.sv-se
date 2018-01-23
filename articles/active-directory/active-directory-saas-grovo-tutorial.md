---
title: "Självstudier: Azure Active Directory-integrering med Grovo | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Grovo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: e4bb050e96d3e8d9da4666f5418ac3e444f6212d
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Självstudier: Azure Active Directory-integrering med Grovo

I kursen får lära du att integrera Grovo med Azure Active Directory (AD Azure).

Integrera Grovo med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Grovo.
- Du kan aktivera användarna att automatiskt hämta loggat in på Grovo (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Grovo, behöver du följande:

- En Azure AD-prenumeration
- En Grovo enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Grovo från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-grovo-from-the-gallery"></a>Att lägga till Grovo från galleriet
Du måste lägga till Grovo från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Grovo i Azure AD.

**Utför följande steg för att lägga till Grovo från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Grovo**väljer **Grovo** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Grovo i resultatlistan](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Grovo baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Grovo motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Grovo upprättas.

I Grovo, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Grovo, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Grovo](#create-a-grovo-test-user)**  – du har en motsvarighet för Britta Simon i Grovo som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Grovo program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Grovo:**

1. I Azure-portalen på den **Grovo** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_samlbase.png)

3. På den **Grovo domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Grovo domän med enkel inloggning information](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

4. Kontrollera **visa avancerade inställningar för URL: en**, utföra följande steg:

    ![URL: er och Grovo domän med enkel inloggning information](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url1.png)

    a. I den **Relay tillstånd** textruta Skriv en URL med följande mönster:`https://<subdomain>.grovo.com`

    b. Om du vill konfigurera programmet i **SP** initierade läge, utför följande steg:

    ![URL: er och Grovo domän med enkel inloggning information](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url2.png)
    
    I den **inloggning URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare, Reply URL på URL: en och Relay tillstånd. Kontakta [Grovo supportteamet](https://www.grovo.com/contact-us) att hämta dessa värden.
 
5. Grovo program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Mappa **användar-ID** med **user.mail** och konfigurera andra attribut enligt nedan skärmbild.
    
    ![Konfigurera enkel inloggning attb](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_attribute.png)
    
6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | Förnamn          | user.givenname |
    | Efternamn           | User.surname |
    | E-postadress       | User.Mail    |
    | EmployeeID          | User.EmployeeID |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.


7. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-grovo-tutorial/tutorial_general_400.png)

9. På den **Grovo Configuration** klickar du på **konfigurera Grovo** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Grovo konfiguration](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_configure.png) 

10. I en annan webbläsarfönstret, logga in på Grovo som administratör.

11. Gå till **Admin** > **integreringar**.
 
    ![Grovo konfiguration](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_admin.png) 

12. Klicka på **konfigurera** under **SP initierade SAML 2.0** avsnitt.

    ![Grovo konfiguration](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_setup.png)

13. I **SP initierade SAML 2.0** popup-fönstret utför följande steg:

    ![Grovo konfiguration](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_saml.png)

    a. I den **enhets-id** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    b. I den **enkel inloggning tjänstslutpunkten** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c. Välj **enkel inloggning på tjänstens slutpunktsbindning** som `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Öppna den hämtade **Base64-kodat certifikat** från Azure-portalen i anteckningar klistrar du in det i den **offentliga nyckel** textruta.

    e. Klicka på **Nästa**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-grovo-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-grovo-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-grovo-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-grovo-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-grovo-test-user"></a>Skapa en testanvändare Grovo

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Grovo. Grovo stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Grovo om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt, kontakta [Grovo supportteamet](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Grovo.

![Tilldela rollen][200] 

**Om du vill tilldela Grovo Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Grovo**.

    ![Länken Grovo i listan med program](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Grovo på åtkomstpanelen du bör få automatiskt loggat in på ditt Grovo program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_203.png

