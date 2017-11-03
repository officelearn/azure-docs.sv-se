---
title: "Självstudier: Azure Active Directory-integrering med iLMS | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 22c72020200138e78835ed7dd2661f18b824c785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Självstudier: Azure Active Directory-integrering med iLMS

I kursen får lära du att integrera iLMS med Azure Active Directory (AD Azure).

Integrera iLMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iLMS
- Du kan aktivera användarna att automatiskt hämta loggat in på iLMS (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med iLMS, behöver du följande:

- En Azure AD-prenumeration
- En iLMS enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till iLMS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ilms-from-the-gallery"></a>Att lägga till iLMS från galleriet
Du måste lägga till iLMS från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av iLMS i Azure AD.

**Utför följande steg för att lägga till iLMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **iLMS**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. Välj i resultatpanelen **iLMS**, klicka på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med iLMS baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i iLMS motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iLMS upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i iLMS.

Om du vill konfigurera och testa Azure AD enkel inloggning med iLMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare iLMS](#creating-an-ilms-test-user)**  – du har en motsvarighet för Britta Simon i iLMS som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt iLMS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iLMS:**

1. I Azure-portalen på den **iLMS** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. På den **iLMS domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. I den **identifierare** textruta klistra in den **identifierare** värdet du kopiera från **tjänstleverantör** SAML-inställningarna i iLMS administrationsportal.

    b. I den **Reply URL** textruta klistra in den **slutpunkt (URL)** värdet du kopiera från **tjänstleverantör** SAML-inställningarna i iLMS administrationsportal med följande mönster`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Den här 123456 är en exempel-värdet för identifieraren.

4. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    I den **inloggnings-URL** textruta klistra in den **slutpunkt (URL)** värdet du kopiera från **tjänstleverantör** SAML inställningarna iLMS administrationsportalen som`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. För att aktivera JIT-etablering, förväntar iLMS programmet SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Skapa **avdelning, Region** och **Division** attribut och Lägg till namnet på attributen i iLMS. Dessa attribut som visas ovan krävs.  

    > [!NOTE] 
    > Du måste aktivera **skapa Un-recognized användarkonto** i iLMS att mappa dessa attribut. Följ instruktionerna [här](http://support.inspiredelearning.com/customer/portal/articles/2204526) att få en uppfattning på attribut-konfigurationen.

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributets namn | Attributvärdet |
    | ---------------| --------------- |    
    | Division | User.Department |
    | Region | User.state |
    | Avdelning | User.jobtitle |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. I en annan webbläsarfönstret, logga in på ditt **iLMS administrationsportalen** som administratör.

10. Klicka på **SSO:SAML** under **inställningar** att öppna inställningarna för SAML och utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Expandera den **tjänstleverantör** avsnitt och kopiera den **identifierare** och **slutpunkt (URL)** värde.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. Under **identitetsleverantör** klickar du på **importera Metadata**.
    
    c. Välj den **Metadata** hämta filen från Azure-portalen från **SAML-signeringscertifikat** avsnitt.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Om du vill aktivera JIT etablering för att skapa iLMS datorkonton för un-känna igen användare, följa nedanstående steg:
        
       - Kontrollera **skapa icke godkänd användarkonto**.
       
       ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mappa attribut i Azure AD med attribut i iLMS. Ange namnet på attribut eller standardvärdet i attributkolumnen.

    e. Gå till **affärsregler** fliken och utför följande steg: 
        
       ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/5.png)

       - Kontrollera **skapa Un-recognized regioner, avdelningar och avdelningar** att skapa regioner, avdelningar och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.
        
       - Kontrollera **uppdatering användarens profil under inloggning** att ange om användarens profil uppdateras med varje enkel inloggning. 
        
       - Om den **”uppdatering tomma värden för ej obligatoriska fält i användarprofil”** alternativet är markerat, valfri profil fält som är tom vid inloggning kommer även att användarprofilen iLMS som innehåller tomma värden för fälten.
        
       - Kontrollera **skicka fel e-postmeddelande** och ange e-postadress för användaren där du vill ta emot e-postaviseringen fel.

11. Klicka på **spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-ilms-test-user"></a>Skapa en testanvändare iLMS

Programmet stöder bara i tid användaretablering och authentication-användare skapas automatiskt i programmet. JIT fungerar om du har klickat på den **skapa Un-recognized användarkonto** kryssrutan under SAML Konfigurationsinställningen på administrationsportalen för iLMS.

Följ nedanstående steg om du behöver skapa en användare manuellt:

1. Logga in på webbplatsen iLMS företag som administratör.

2. Klicka på **”registrera användare”** under **användare** att öppna **registrera användaren** sidan. 
   
   ![Lägga till medarbetare](./media/active-directory-saas-ilms-tutorial/3.png)

3. På den **”registrera användare”** utför följande steg.

    ![Lägga till medarbetare](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. I den **Förnamn** textruta första typnamn Britta.
   
    b. I den **efternamn** textruta anger efternamn Simon.

    c. I den **e-post-ID** textruta skriver Britta Simon konto e-postadress.

    d. I den **Region** listrutan, Välj värdet för region.

    e. I den **Division** listrutan, Välj värdet för delning.

    f. I den **avdelning** listrutan, Välj värdet för avdelning.

    g. Klicka på **Spara**.

    > [!NOTE] 
    > Du kan skicka e-post för registrering till användare genom att välja **skicka e-post från registrering** kryssrutan.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till iLMS.

![Tilldela användare][200] 

**Om du vill tilldela iLMS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **iLMS**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen iLMS på åtkomstpanelen du bör få automatiskt loggat in på ditt iLMS program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

