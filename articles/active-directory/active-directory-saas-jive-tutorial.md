---
title: "Självstudier: Azure Active Directory-integrering med Jive | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 272b2697d59ee1ec961db6cdd13231030697d1d1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Självstudier: Azure Active Directory-integrering med Jive

I kursen får lära du att integrera Jive med Azure Active Directory (AD Azure).

Integrera Jive med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jive
- Du kan aktivera användarna att automatiskt hämta loggat in på Jive (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Jive, behöver du följande:

- En Azure AD-prenumeration
- En Jive enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Jive från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jive-from-the-gallery"></a>Att lägga till Jive från galleriet
Du måste lägga till Jive från galleriet i listan över hanterade SaaS-appar för att konfigurera Jive till Azure AD-integrering.

**Utför följande steg för att lägga till Jive från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Jive**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_search.png)

5. Välj i resultatpanelen **Jive**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Jive baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Jive motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Jive upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Jive.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jive, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Jive](#creating-a-jive-test-user)**  – du har en motsvarighet för Britta Simon i Jive som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jive program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jive:**

1. I Azure-portalen på den **Jive** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-jive-tutorial/tutorial_jive_samlbase.png)

3. På den **Jive domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jive-tutorial/tutorial_jive_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<instance name>.jivecustom.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<instance name>.jiveon.com`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Jive klienten supportteamet](https://www.jivesoftware.com/services-support/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jive-tutorial/tutorial_jive_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jive-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **Jive** sida, inloggning till din Jive klient som administratör.

7. Klicka på menyn högst upp ”**Saml**”.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    a. Välj **aktiverad** under den **allmänna** fliken.   
    b. Klicka på ”**spara alla inställningar för saml**” knappen.

8. Navigera till den ”**Idp Metadata**” fliken.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. Kopiera innehållet i den hämta metadata XML-filen och klistrar in det i den **identitet Provider (IDP) Metadata** textruta.
    
    b. Klicka på ”**spara alla inställningar för saml**” knappen. 

9. Gå till den ”**mappning av attributet**” fliken.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. I den **e-post** textruta, kopiera och klistra in attributnamnet för **e** värde.
   
    b. I den **Förnamn** textruta, kopiera och klistra in attributnamnet för **givenname** värde.
   
    c. I den **efternamn** textruta, kopiera och klistra in attributnamnet för **efternamn** värde.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jive-test-user"></a>Skapa en testanvändare Jive

Arbeta med [Jive klienten supportteamet](https://www.jivesoftware.com/services-support/) att lägga till användare i Jive-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jive.

![Tilldela användare][200] 

**Om du vill tilldela Jive Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Jive**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jive-tutorial/tutorial_jive_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Jive på åtkomstpanelen du bör få automatiskt loggat in på ditt Jive program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png

