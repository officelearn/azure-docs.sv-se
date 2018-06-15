---
title: 'Självstudier: Azure Active Directory-integrering med Work.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: bfc76d05a52d0283e3367f9c98dc8ed427cbe592
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354106"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Självstudier: Azure Active Directory-integrering med Work.com

I kursen får lära du att integrera Work.com med Azure Active Directory (AD Azure).

Integrera Work.com med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Work.com
- Du kan aktivera användarna att automatiskt hämta loggat in på Work.com (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Work.com, behöver du följande:

- En Azure AD-prenumeration
- En Work.com enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till Work.com från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-workcom-from-the-gallery"></a>Lägg till Work.com från galleriet
Du måste lägga till Work.com från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Work.com i Azure AD.

**Utför följande steg för att lägga till Work.com från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Work.com**väljer **Work.com** från resultatrutan Klicka **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Work.com baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Work.com motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Work.com upprättas.

I Work.com, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Work.com, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Work.com](#create-a-workcom-test-user)**  – du har en motsvarighet för Britta Simon i Work.com som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Work.com program.

>[!NOTE]
>Om du vill konfigurera enkel inloggning måste du konfigurera ett anpassat domännamn Work.com ännu. Du måste ange minst ett domännamn, testa ditt domännamn och distribuera den till hela organisationen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Work.com:**

1. I Azure-portalen på den **Work.com** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. På den **Work.com domän och URL: er** avsnittet, utför följande:

    ![Avsnittet Work.com domän och URL: er](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Work.com klienten supportteamet](https://help.salesforce.com/articleView?id=000159855&type=3) att hämta det här värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Klicka på **spara** knappen.

    ![Knappen Spara](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. På den **Work.com Configuration** klickar du på **konfigurera Work.com** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Work.com konfigurationsavsnitt](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Logga in på din Work.com klient som administratör.

8. Gå till **installationsprogrammet**.
   
    ![Installationsprogrammet](./media/active-directory-saas-work-com-tutorial/ic794108.png "installationen")

9. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
   
    ![Min domän](./media/active-directory-saas-work-com-tutorial/ic767825.png "min domän")

10. Om du vill verifiera att din domän har ställts in korrekt, se till att den är i ”**steg 4 distribueras till användarna**” och granska din ”**Mina Domäninställningar**”.
   
    ![Domänen som distribuerats till användaren](./media/active-directory-saas-work-com-tutorial/ic784377.png "domän som har distribuerats till användaren")

11. Logga in på din Work.com-klient.

12. Gå till **installationsprogrammet**.
    
    ![Installationsprogrammet](./media/active-directory-saas-work-com-tutorial/ic794108.png "installationen")

13. Expandera den **säkerhetsåtgärder** -menyn och klicka sedan på **inställningar för enkel inloggning**.
    
    ![Enkel inloggning inställningar](./media/active-directory-saas-work-com-tutorial/ic794113.png "enkel inloggning inställningar")

14. På den **inställningar för enkel inloggning** dialogrutan utför följande steg:
    
    ![SAML aktiverat](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML aktiverad")
    
    a. Välj **SAML aktiverat**.
    
    b. Klicka på **Ny**.

15. I den **SAML enkel inloggning inställningar** avsnittet, utför följande steg:
    
    ![SAML enkel inloggning inställningen](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML enkel inloggning inställningen")
    
    a. I den **namn** textruta, ange ett namn för din konfiguration.  
       
    > [!NOTE]
    > Att tillhandahålla ett värde för **namn** automatiskt fylla i **API-namnet** textruta.
    
    b. I **utfärdaren** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
    
    c. Om du vill överföra hämtat certifikat från Azure-portalen klickar du på **Bläddra**.
    
    d. I den **enhets-Id** textruta typen `https://salesforce-work.com`.
    
    e. Som **SAML identitetstyp**väljer **Assertion innehåller Federation-ID från användarobjektet**.
    
    f. Som **SAML identitet plats**väljer **identitet är i elementet NameIdentfier i instruktionen ämne**.
    
    g. I **identitet providern inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    h. I **identitet providern logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
    
    i. Som **providern initierade begära Tjänstbindning**väljer **HTTP Post**.
    
    j. Klicka på **Spara**.

16. I din Work.com klassiska portalen i det vänstra navigeringsfönstret klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
    
    ![Min domän](./media/active-directory-saas-work-com-tutorial/ic794115.png "min domän")

17. På den **min domän** sidan den **inloggningen sidan anpassning** klickar du på **redigera**.
    
    ![Inloggningssidan anpassning](./media/active-directory-saas-work-com-tutorial/ic767826.png "inloggningssidan anpassning")

14. På den **inloggningen sidan anpassning** sidan den **Autentiseringstjänsten** avsnitt, namnet på din **SAML SSO inställningar** visas. Markera den och klicka sedan på **spara**.
    
    ![Inloggningssidan anpassning](./media/active-directory-saas-work-com-tutorial/ic784366.png "inloggningssidan anpassning")

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägg till](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan Användarsida](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workcom-test-user"></a>Skapa en testanvändare Work.com
För Azure Active Directory-användare för att kunna logga in, måste de etableras till Work.com. När det gäller Work.com är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
1. Logga in på webbplatsen Work.com företag som administratör.

2. Gå till **installationsprogrammet**.
   
    ![Installationsprogrammet](./media/active-directory-saas-work-com-tutorial/IC794108.png "installationen")
3. Gå till **hantera användare \> användare**.
   
    ![Hantera användare](./media/active-directory-saas-work-com-tutorial/IC784369.png "hantera användare")

4. Klicka på **ny användare**.
   
    ![Alla användare](./media/active-directory-saas-work-com-tutorial/IC794117.png "alla användare")

5. I avsnittet användare redigera utför följande steg i attribut för ett giltigt Azure AD-kontot som du vill etablera i relaterade textrutor:
   
    ![Redigera användare](./media/active-directory-saas-work-com-tutorial/ic794118.png "Redigera användare")
   
    a. I den **Förnamn** textruta typ av **Förnamn** användarens **Britta**.
    
    b. I den **efternamn** textruta typ av **efternamn** användarens **Simon**.
    
    c. I den **Alias** textruta typ av **namn** användarens **BrittaS**.
    
    d. I den **e-post** textruta typ av **e-postadress** för användare **Brittasimon@contoso.com**.
    
    e. I den **användarnamn** textruta, Skriv ett användarnamn för användaren som **Brittasimon@contoso.com**.
    
    f. I den **smeknamn** textruta typ a **smeknamn** för användare **Simon**.
    
    g. Välj **rollen**, **användarlicens**, och **profil**.
    
    h. Klicka på **Spara**.  
      
    > [!NOTE]
    > Azure AD-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan den aktiveras.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Work.com.

![Tilldela användare][200] 

**Om du vill tilldela Work.com Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Work.com**.

    ![Work.com i appens lista](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Work.com på åtkomstpanelen du bör få automatiskt loggat in på ditt Work.com program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

