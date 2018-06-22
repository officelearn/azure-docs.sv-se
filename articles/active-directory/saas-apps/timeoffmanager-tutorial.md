---
title: 'Självstudier: Azure Active Directory-integrering med TimeOffManager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 4813c492ba25b0d6dd524e2c0b4b5b6e8c2a2bfa
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308249"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Självstudier: Azure Active Directory-integrering med TimeOffManager

I kursen får lära du att integrera TimeOffManager med Azure Active Directory (AD Azure).

Integrera TimeOffManager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TimeOffManager
- Du kan aktivera användarna att automatiskt hämta loggat in på TimeOffManager (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med TimeOffManager, behöver du följande:

- En Azure AD-prenumeration
- En TimeOffManager enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till TimeOffManager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-timeoffmanager-from-the-gallery"></a>Lägg till TimeOffManager från galleriet
Du måste lägga till TimeOffManager från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TimeOffManager i Azure AD.

**Utför följande steg för att lägga till TimeOffManager från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TimeOffManager**väljer **TimeOffManager** från resultatet Kontrollpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TimeOffManager baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TimeOffManager motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TimeOffManager upprättas.

I TimeOffManager, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TimeOffManager, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TimeOffManager](#create-a-timeoffmanager-test-user)**  – du har en motsvarighet för Britta Simon i TimeOffManager som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TimeOffManager program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TimeOffManager:**

1. I Azure-portalen på den **TimeOffManager** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserade inloggning](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. På den **TimeOffManager domän och URL: er** avsnittet, utför följande:

     ![Avsnittet TimeOffManager domän och URL: er](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Du kan hämta värdet från **inställningssidan för enkelinloggning** som beskrivs senare i självstudiekursen eller kontakta [TimeOffManager supportteamet](https://www.purelyhr.com/contact-us).
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. Syftet med det här avsnittet är att beskriva hur användarna att autentisera till TimeOffManger med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.
    
    Tillämpningsprogrammet TimeOffManger förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här.

    ![SAML-token attribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "attribut för saml-token")
    
    | Attributnamn | Attributvärde |
    | --- | --- |
    | Förnamn |User.givenName |
    | Efternamn |User.surname |
    | E-post |User.Mail |
    
    a.  För varje datarad i tabellen ovan, klickar du på **lägga till användarattribut**.
    
    ![SAML-token attribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "attribut för saml-token")
    
    ![SAML-token attribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "attribut för saml-token")
    
    b.  I den **attributnamn** textruta ange attributets namn visas för den raden.
    
    c.  I den **attributvärdet** textruta Välj attributvärde som visas för den raden.
    
    d.  Klicka på **OK**.
    
6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/timeoffmanager-tutorial/tutorial_general_400.png)

7. På den **TimeOffManager Configuration** klickar du på **konfigurera TimeOffManager** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![TimeOffManager konfigurationsavsnitt](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. Logga in på webbplatsen TimeOffManager företag som en administratör i en annan webbläsarfönster.

9. Gå till **konto \> konto alternativ \> enkel inloggning inställningar**.
   
   ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795917.png "enkel inloggning inställningar")
7. I den **inställningar för enkel inloggning** avsnittet, utför följande steg:
   
   ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795918.png "enkel inloggning inställningar")
   
   a. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet till **X.509-certifikat** textruta.
   
   b. I **Idp utfärdaren** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
   
   c. I **IdP slutpunkts-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
   d. Som **genomdriva SAML**väljer **nr**.
   
   e. Som **skapa automatiskt användare**väljer **Ja**.
   
   f. I **logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
   
   g. Klicka på **spara ändringar**.

11. I **inställningar för enkelinloggning** sidan, kopierar du värdet för **Assertion konsument-tjänstens URL** och klistra in den i den **Reply URL** textrutan under **TimeOffManager Domänen och URL: er** avsnitt i Azure-portalen. 

      ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795915.png "enkel inloggning inställningar")

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Användare och grupper--> alla användare](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan Användarsida](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Skapa en testanvändare TimeOffManager

För att aktivera Azure AD-användare att logga in på TimeOffManager etableras de för TimeOffManager.  

TimeOffManager stöder bara i tid användaretablering. Det finns ingen åtgärd-objekt.  

Användare läggs till automatiskt under den första inloggningen med enkel inloggning på.

>[!NOTE]
>Du kan använda något annat TimeOffManager användarens konto skapas verktyg eller API: er som tillhandahålls av TimeOffManager att etablera Azure AD-användarkonton.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TimeOffManager.

![Tilldela användare][200] 

**Om du vill tilldela TimeOffManager Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TimeOffManager**.

    ![TimeOffManager i listan över appar](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TimeOffManager på åtkomstpanelen du bör få automatiskt loggat in på ditt TimeOffManager program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

