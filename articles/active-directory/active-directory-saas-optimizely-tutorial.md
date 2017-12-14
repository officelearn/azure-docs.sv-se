---
title: "Självstudier: Azure Active Directory-integrering med Optimizely | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Optimizely."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e44e1621632bf2c8a3c4050b718d2721f5132d06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Självstudier: Azure Active Directory-integrering med Optimizely

I kursen får lära du att integrera Optimizely med Azure Active Directory (AD Azure).

Integrera Optimizely med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Optimizely
- Du kan aktivera användarna att automatiskt hämta loggat in på Optimizely (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Optimizely, behöver du följande:

- En Azure AD-prenumeration
- En Optimizely enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Optimizely från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-optimizely-from-the-gallery"></a>Att lägga till Optimizely från galleriet
Du måste lägga till Optimizely från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Optimizely i Azure AD.

**Utför följande steg för att lägga till Optimizely från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Optimizely**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. Välj i resultatpanelen **Optimizely**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Optimizely baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Optimizely motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Optimizely upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Optimizely.

Om du vill konfigurera och testa Azure AD enkel inloggning med Optimizely, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Optimizely](#creating-an-optimizely-test-user)**  – du har en motsvarighet för Britta Simon i Optimizely som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Optimizely program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Optimizely:**

1. I Azure-portalen på den **Optimizely** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. På den **Optimizely domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://app.optimizely.net/<instance name>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Du uppdaterar värdet med det faktiska inloggnings-URL och identifierare som beskrivs senare i självstudierna. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. På den **Optimizely Configuration** klickar du på **konfigurera Optimizely** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Konfigurera enkel inloggning på **Optimizely** sida, kontakta din Kontoansvariga Optimizely och ange den hämtade **certifikat (Base64)**, och **SAML inloggning tjänst-URL för enkel**. 

8. Som svar på din e-post ger Optimizely dig logga på URL: en (SP-initierad SSO) och identifierarvärdena (Service Provider enhets-ID).

    a. Kopiera den **SP-initierad SSO URL** tillhandahållna av Optimizely och klistra in i den **logga URL** TextBox-kontroll i **Optimizely domän och URL: er** avsnitt på Azure-portalen 

    b. Kopiera den **Service Provider enhets-ID** tillhandahållna av Optimizely och klistra in i den **identifierare** TextBox-kontroll i **Optimizely domän och URL: er** avsnitt på Azure-portalen 

9. I ett annat webbläsarfönster inloggning i tillämpningsprogrammet Optimizely.

10. Klicka på du kontonamn i övre högra hörnet och sedan **kontoinställningar**.
   
    ![Azure AD-Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. Markera kryssrutan på fliken konto **aktivera enkel inloggning** under enkel inloggning i den **översikt** avsnitt.
   
    ![Azure AD-Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Klicka på **spara**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av Britta Simon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-optimizely-test-user"></a>Skapa en testanvändare Optimizely

I det här avsnittet skapar du en användare som kallas Britta Simon i Optimizely.

1. På startsidan, Välj **medarbetare** fliken.

2. Klicka på Lägg till ny deltagare i projektet, **ny deltagare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Fyll i e-postadressen och tilldela dem en roll. Klicka på **bjuda in**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. De får ett e-inbjudan. Med den e-postadressen som de behöver logga in på Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Optimizely.

![Tilldela användare][200] 

**Om du vill tilldela Optimizely Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Optimizely**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Optimizely på åtkomstpanelen du bör få automatiskt loggat in på ditt Optimizely program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

