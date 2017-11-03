---
title: "Självstudier: Azure Active Directory-integrering med TalentLMS | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TalentLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: f28d6fbfad9dae578a20db7218b7e3b174ed859c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Självstudier: Azure Active Directory-integrering med TalentLMS

I kursen får lära du att integrera TalentLMS med Azure Active Directory (AD Azure).

Integrera TalentLMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TalentLMS
- Du kan aktivera användarna att automatiskt hämta loggat in på TalentLMS (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med TalentLMS, behöver du följande:

- En Azure AD-prenumeration
- En TalentLMS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till TalentLMS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-talentlms-from-the-gallery"></a>Att lägga till TalentLMS från galleriet
Du måste lägga till TalentLMS från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TalentLMS i Azure AD.

**Utför följande steg för att lägga till TalentLMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TalentLMS**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_search.png)

5. Välj i resultatpanelen **TalentLMS**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TalentLMS baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TalentLMS motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TalentLMS upprättas.

I TalentLMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TalentLMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TalentLMS](#creating-a-talentlms-test-user)**  – du har en motsvarighet för Britta Simon i TalentLMS som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TalentLMS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TalentLMS:**

1. I Azure-portalen på den **TalentLMS** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_samlbase.png)

3. På den **TalentLMS domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<tenant-name>.TalentLMSapp.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [TalentLMS klienten supportteamet](https://www.talentlms.com/contact) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värde från certifikatet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_general_400.png)

6. På den **TalentLMS Configuration** klickar du på **konfigurera TalentLMS** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_configure.png)  

7. I en annan webbläsarfönster loggar du in på webbplatsen TalentLMS företag som administratör.

8. I den **konto & inställningar** klickar du på den **användare** fliken.
   
    ![Konto & inställningar](./media/active-directory-saas-talentlms-tutorial/IC777296.png "konto & inställningar")

9. Klicka på **enkel inloggning (SSO)**,

10. Utför följande steg i avsnittet enkel inloggning:
   
    ![Enkel inloggning](./media/active-directory-saas-talentlms-tutorial/IC777297.png "enkel inloggning")   

    a. Från den **SSO integration typen** väljer **SAML 2.0**.

    b. I den **identitetsprovider (IDP)** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.
 
    c. Klistra in den **tumavtrycket** värdet från Azure-portalen i den **certifikat fingeravtryck** textruta.    

    d.  I den **Remote inloggning URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
 
    e. I den **extern URL för utloggning** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    f. Fyll i följande: 

    * I den **TargetedID** textruta typ`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * I den **Förnamn** textruta typ`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * I den **efternamn** textruta typ`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * I den **e-post** textruta typ`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
11. Klicka på **Spara**.
 
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-talentlms-test-user"></a>Skapa en testanvändare TalentLMS

Om du vill aktivera Azure AD-användare kan logga in på TalentLMS etableras de i TalentLMS. När det gäller TalentLMS är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **TalentLMS** klient.

2. Klicka på **användare**, och klicka sedan på **Lägg till användare**.

3. På den **Lägg till användare** dialogrutan utför följande steg:
   
    ![Lägg till användare](./media/active-directory-saas-talentlms-tutorial/IC777299.png "lägga till användare")  

    a. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textruta Ange efternamn för användaren som **Simon**.
 
    c. I den **e-postadress** textruta ange e-postadress för användaren som  **brittasimon@contoso.com** .

    d. Klicka på **lägga till användare**.

>[!NOTE]
>Du kan använda något annat TalentLMS användarens konto skapas verktyg eller API: er som tillhandahålls av TalentLMS att etablera AAD-användarkonton.
 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TalentLMS.

![Tilldela användare][200] 

**Om du vill tilldela TalentLMS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TalentLMS**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TalentLMS på åtkomstpanelen du bör få automatiskt loggat in på ditt TalentLMS program

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_203.png

