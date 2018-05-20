---
title: 'Självstudier: Azure Active Directory-integrering med Wdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: c55b9c32ccf6712da69c869a6f44c71e1c209627
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Självstudier: Azure Active Directory-integrering med Wdesk

I kursen får lära du att integrera Wdesk med Azure Active Directory (AD Azure).

Integrera Wdesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Wdesk
- Du kan aktivera användarna att automatiskt hämta loggat in på Wdesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns i. [Vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Wdesk, behöver du följande:

- En Azure AD-prenumeration
- En Wdesk enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Wdesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-wdesk-from-the-gallery"></a>Att lägga till Wdesk från galleriet
Du måste lägga till Wdesk från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Wdesk i Azure AD.

**Utför följande steg för att lägga till Wdesk från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Wdesk**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. Välj i resultatpanelen **Wdesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Wdesk baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Wdesk motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Wdesk upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Wdesk.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wdesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Wdesk](#creating-a-wdesk-test-user)**  – du har en motsvarighet för Britta Simon i Wdesk som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Wdesk program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Wdesk:**

1. I Azure-portalen på den **Wdesk** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. På den **Wdesk domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Du kan få värdena från WDesk portal när du konfigurerar SSO. 
  
5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. I en annan webbläsarfönstret, logga in på Wdesk som en administratör.

8. I nederkant vänstra hörnet, klickar du på **Admin** och välj **kontoadministratören**:
 
     ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. I Wdesk Admin, navigerar du till **säkerhet**, sedan **SAML** > **SAML inställningar**:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. Under **allmänna inställningar**, kontrollera den **aktivera SAML enkel inloggning**:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. Under **information om Service Provider**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiera den **Inloggningswebbadressen** och klistra in den i **inloggnings-Url** textruta på Azure-portalen.
   
      b. Kopiera den **Url för tjänstmetadata** och klistra in den i **identifierare** textruta på Azure-portalen.
       
      c. Kopiera den **konsumenten url** och klistra in den i **Reply Url** textruta på Azure-portalen.
   
      d. Klicka på **spara** på Azure-portalen för att spara ändringarna.      

12. Klicka på **konfigurera inställningarna för IdP** att öppna **redigera inställningar för IdP** dialogrutan. Klicka på **Välj fil** att hitta den **Metadata.xml** fil som du sparade från Azure-portalen sedan ladda upp den.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Klicka på **spara ändringar**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-wdesk-test-user"></a>Skapa en testanvändare Wdesk

Om du vill aktivera Azure AD-användare kan logga in på Wdesk etableras de i Wdesk. I Wdesk är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på Wdesk som en administratör.
2. Gå till **Admin** > **konto Admin**.

     ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicka på **medlemmar** under **personer**.

4. Klicka på **Lägg till medlem** att öppna **Lägg till medlem** dialogrutan. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. I **användare** text Ange användarnamnet för användaren som **brittasimon@contoso.com** och på **Fortsätt** knappen.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Ange information som visas nedan:
  
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. I **e-post** text Ange e-postadressen för användaren som **brittasimon@contoso.com**.

    b. I **Förnamn** text Ange först namnet på användaren som **Britta**.

    c. I **efternamn** text Ange efternamn för användaren som **Simon**.

7. Klicka på **spara medlemmen** knappen.  

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wdesk.

![Tilldela användare][200] 

**Om du vill tilldela Wdesk Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Wdesk**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Wdesk på åtkomstpanelen du bör få automatiskt loggat in på ditt Wdesk program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

