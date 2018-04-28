---
title: 'Självstudier: Azure Active Directory-integrering med LearnUpon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 2796f280b2f95504be5f9691098bff07fdd93393
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Självstudier: Azure Active Directory-integrering med LearnUpon

I kursen får lära du att integrera LearnUpon med Azure Active Directory (AD Azure).

Integrera LearnUpon med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LearnUpon
- Du kan aktivera användarna att automatiskt hämta loggat in på LearnUpon (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med LearnUpon, behöver du följande:

- En Azure AD-prenumeration
- En LearnUpon enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till LearnUpon från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-learnupon-from-the-gallery"></a>Att lägga till LearnUpon från galleriet
Du måste lägga till LearnUpon från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LearnUpon i Azure AD.

**Utför följande steg för att lägga till LearnUpon från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LearnUpon**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. Välj i resultatpanelen **LearnUpon**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LearnUpon baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LearnUpon motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LearnUpon upprättas.

I LearnUpon, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med LearnUpon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LearnUpon](#creating-a-learnupon-test-user)**  – du har en motsvarighet för Britta Simon i LearnUpon som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LearnUpon program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LearnUpon:**

1. I Azure-portalen på den **LearnUpon** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. På den **LearnUpon domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera det här värdet med det faktiska Reply-URL. Att hämta det här värdet Kontakta [LearnUpon supportteamet](https://www.learnupon.com/features/support/).



4. På den **SAML-signeringscertifikat** avsnittet, leta upp den **tumavtrycket** – detta kommer att läggas till dina LearnUpon SAML-inställningar.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. På den **LearnUpon Configuration** klickar du på **konfigurera LearnUpon** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Öppna en annan instans för webbläsaren och logga in till LearnUpon med ett administratörskonto. 

8. Klicka på den **inställningar** fliken.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Klicka på **Single Sign On - SAML**, och klicka sedan på **allmänna inställningar** att konfigurera inställningar för SAML.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. I den **allmänna inställningar** avsnittet, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Välj **aktiverat**.

    b. Välj **Version** som **2.0**.

    c. Välj **hoppa över villkor** som **nr**.

    d. I den **SAML-Token efter param namnet** textruta typen namnet på begäran post parameter till URL: en för SAML konsumenten ovan som innehåller SAML-kontroll för att verifiera och autentiserad – till exempel **SAMLResponse**.

    e. I den **identifierare namnformat** textruta typ - exempelvis det värde som anger var i SAML-kontroll användare identifierare (e-postadress) finns **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**.
  
    f. I den **identifiera leverantörsplatsen** textruta Skriv det värde som anger där användarna skickas till om de klickar på överförda ikon från Azure portal inloggningsskärmen.
  
    g. I den **logga ut URL** textruta klistra in den **Sign-Out URL** som du har kopierat från Azure-portalen.
    
    h. Klicka på **hantera fingeravtrycksläsare utskrifter**, och sedan ladda upp fingeravtryck av hämtade certifikatet.

11. Klicka på **användarinställningar**, och utför sedan följande steg:
   
     ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. I den **förnamn identifierare Format** textruta typ - exempelvis värdet som talar om för oss var i SAML-kontroll användare firstname finns: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. I den **senaste namnformat identifierare** textruta typ - exempelvis värdet som talar om för oss var i SAML-kontroll användare efternamn finns: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-learnupon-test-user"></a>Skapa en testanvändare LearnUpon

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i LearnUpon. LearnUpon stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas vid ett försök att komma åt LearnUpon om den inte finns. [Konfigurera Azure AD-Single Sign-On](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Om du behöver skapa en användare manuellt måste du kontakta [LearnUpon supportteamet](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LearnUpon.

![Tilldela användare][200] 

**Om du vill tilldela LearnUpon Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **LearnUpon**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LearnUpon på åtkomstpanelen du bör få automatiskt loggat in på ditt LearnUpon program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png

