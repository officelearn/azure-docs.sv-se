---
title: "Självstudier: Azure Active Directory-integrering med Salesforce | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Självstudier: Azure Active Directory-integrering med Salesforce

I kursen får lära du att integrera Salesforce med Azure Active Directory (AD Azure).

Integrera Salesforce med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce
- Du kan aktivera användarna att automatiskt hämta loggat in till Salesforce (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Salesforce, behöver du följande:

- En Azure AD-prenumeration
- En Salesforce enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Salesforce från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-from-the-gallery"></a>Att lägga till Salesforce från galleriet
Du måste lägga till Salesforce från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Salesforce i Azure AD.

**Utför följande steg för att lägga till Salesforce från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Salesforce**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. Välj i resultatpanelen **Salesforce**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Salesforce baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Salesforce motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Salesforce.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Salesforce](#creating-a-salesforce-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce:**

1. I Azure-portalen på den **Salesforce** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. På den **Salesforce-domänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster: 
   * Enterprise-konto:`https://<subdomain>.my.salesforce.com`
   * Utvecklarkonto för:`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska inloggnings-URL. Kontakta [Salesforce klienten supportteamet](https://help.salesforce.com/support) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. På den **Salesforce Configuration** klickar du på **konfigurera Salesforce** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.** 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

8.  Under den **administratör** navigeringsfönstret klickar du på **säkerhetsåtgärder** att expandera avsnittet relaterade. Klicka på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.
    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Om det inte går att aktivera enkel inloggning inställningarna för ditt Salesforce-konto kan du behöva kontakta [Salesforce klienten supportteamet](https://help.salesforce.com/support). 

10. Välj **SAML aktiverat**, och klicka sedan på **spara**.

      ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Om du vill konfigurera SAML enkel inloggning för, klickar du på **ny**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. På den **SAML enkel inloggning inställningen redigera** kontrollerar du följande konfigurationer:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. För den **namnet** skriver du ett eget namn för den här konfigurationen. Att tillhandahålla ett värde för **namn** automatiskt fylla i **API-namnet** textruta.

    b. Klistra in **SMAL enhets-ID** värde i den **utfärdaren** i Salesforce.

    c. I den **enhets-Id textruta**, ange ditt Salesforce-domännamn med hjälp av följande mönster:
      
      * Enterprise-konto:`https://<subdomain>.my.salesforce.com`
      * Utvecklarkonto för:`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Klicka på **Bläddra** eller **Välj fil** att öppna den **Välj fil för uppladdning** dialogrutan Välj ditt Salesforce-certifikat och klicka sedan på **öppna** att ladda upp certifikatet.

    e. För **SAML identitetstyp**väljer **Assertion innehåller användarens salesforce.com användarnamn**.

    f. För **SAML identitet plats**väljer **identitet är i elementet NameIdentifier i instruktionen ämne**

    g. Klistra in **inloggning tjänst-URL för enkel** till den **identitet providern inloggnings-URL** i Salesforce.
    
    h. För **providern initierade begära Tjänstbindning**väljer **HTTP-omdirigering**.
    
    Jag. Klicka slutligen på **spara** tillämpa SAML enkel inloggning inställningarna.

13. Klicka på det vänstra navigeringsfönstret i Salesforce **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Rulla ned till den **Autentiseringskonfiguration** avsnittet och klicka på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. I den **Autentiseringstjänsten** avsnittet väljer du ett eget namn för konfigurationen av SAML SSO och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Om mer än en autentiseringstjänst är markerat uppmanas användarna att välja vilka Autentiseringstjänsten som de vill logga in med vid initiering av enkel inloggning till Salesforce-miljön. Om du inte vill att det ska ske, så du bör **lämnar du alla andra autentiseringstjänster avmarkerat**.
<CE>    
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I det vänstra navigeringsfönstret i den **Azure-portalen**, klickar du på **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-salesforce-test-user"></a>Skapa en testanvändare Salesforce

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce. Salesforce stöder just-in-time-allokering som är aktiverad som standard.
Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Salesforce, skapas en ny när du försöker komma åt Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce.

![Tilldela användare][200] 

**Om du vill tilldela Salesforce Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Salesforce**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen på [https://myapps.microsoft.com](https://myapps.microsoft.com/)sedan logga in i testkonto och klicka på **Salesforce**.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

