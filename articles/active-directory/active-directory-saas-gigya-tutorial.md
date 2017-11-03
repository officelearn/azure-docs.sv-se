---
title: "Självstudier: Azure Active Directory-integrering med Gigya | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Gigya."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: b65a33989a045a3e0b57fda522a9bc3b0770c7f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Självstudier: Azure Active Directory-integrering med Gigya

I kursen får lära du att integrera Gigya med Azure Active Directory (AD Azure).

Integrera Gigya med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Gigya
- Du kan aktivera användarna att automatiskt hämta loggat in på Gigya (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Gigya, behöver du följande:

- En Azure AD-prenumeration
- En Gigya enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Gigya från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-gigya-from-the-gallery"></a>Att lägga till Gigya från galleriet
Du måste lägga till Gigya från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Gigya i Azure AD.

**Utför följande steg för att lägga till Gigya från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Gigya**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_search.png)

5. Välj i resultatpanelen **Gigya**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Gigya baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Gigya motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Gigya upprättas.

I Gigya, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Gigya, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Gigya](#creating-a-gigya-test-user)**  – du har en motsvarighet för Britta Simon i Gigya som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Gigya program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Gigya:**

1. I Azure-portalen på den **Gigya** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_samlbase.png)

3. På den **Gigya domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`http://<companyname>.gigya.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Gigya klienten supportteamet](https://www.gigya.com/support-policy/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_general_400.png)

6. På den **Gigya Configuration** klickar du på **konfigurera Gigya** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_configure.png) 

7. Logga in på webbplatsen Gigya företag som en administratör i en annan webbläsarfönster.

8. Gå till **inställningar \> SAML inloggningen**, och klicka sedan på den **Lägg till** knappen.
   
    ![SAML-inloggningen](./media/active-directory-saas-gigya-tutorial/ic789532.png "SAML-inloggning")

9. I den **SAML inloggningen** avsnittet, utför följande steg:
   
    ![SAML-konfiguration](./media/active-directory-saas-gigya-tutorial/ic789533.png "SAML-konfiguration")
   
    a. I den **namn** textruta, ange ett namn för din konfiguration.
   
    b. I **utfärdaren** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen. 
   
    c. I **inloggning tjänst-URL för enkel** textruta klistra in värdet för **inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    d. I **Format för namn-ID** textruta klistra in värdet för **identifierare namnformat** som du har kopierat från Azure-portalen.
   
    e. Öppna din Base64-kodade certifikatet i anteckningar som hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta.
   
    f. Klicka på **Spara inställningar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-gigya-test-user"></a>Skapa en testanvändare Gigya

För att aktivera Azure AD-användare att logga in på Gigya etableras de i Gigya.  
När det gäller Gigya är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att etablera en användarkonton:

1. Logga in på ditt **Gigya** företagets webbplats som administratör.

2. Gå till **Admin \> hantera användare**, och klicka sedan på **bjuda in användare**.
   
    ![Hantera användare](./media/active-directory-saas-gigya-tutorial/ic789535.png "hantera användare")

3. I dialogrutan bjuda in användare utför du följande steg:
   
    ![Bjuda in användare](./media/active-directory-saas-gigya-tutorial/ic789536.png "bjuda in användare")
   
    a. I den **e-post** textruta skriver du e postalias för ett giltigt Azure Active Directory-konto som du vill etablera.
    
    b. Klicka på **bjuda in användare**.
      
    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan den aktiveras.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Gigya.

![Tilldela användare][200] 

**Om du vill tilldela Gigya Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Gigya**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Gigya på åtkomstpanelen du bör få automatiskt loggat in på ditt Gigya program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_203.png

