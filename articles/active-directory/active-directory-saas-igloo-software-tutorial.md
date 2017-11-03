---
title: "Självstudier: Azure Active Directory-integrering med Igloo programvara | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Igloo programvaran och Azure Active Directory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ab3891e11eb33b4d233e4fc967a40c7df06e4f4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Självstudier: Azure Active Directory-integrering med Igloo programvara

I kursen får lära du att integrera Igloo programvara med Azure Active Directory (AD Azure).

Integrera Igloo programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Igloo programvara
- Du kan aktivera användarna att automatiskt hämta loggat in på Igloo programvara (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Igloo programvara, behöver du följande:

- En Azure AD-prenumeration
- En Igloo programvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Igloo programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-igloo-software-from-the-gallery"></a>Lägga till Igloo programvara från galleriet
Du måste lägga till Igloo programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Igloo programvara i Azure AD.

**Utför följande steg för att lägga till Igloo programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Igloo programvara**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. Välj i resultatpanelen **Igloo programvara**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Igloo programvara baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Igloo programvara till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Igloo programvara upprättas.

I Igloo programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Igloo programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Igloo programvara](#creating-an-igloo-software-test-user)**  – du har en motsvarighet för Britta Simon Igloo programvara som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Igloo programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Igloo programvara:**

1. I Azure-portalen på den **Igloo programvara** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. På den **Igloo programvara domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<company name>.igloocommmunities.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<company name>.igloocommmunities.com/saml.digest`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [Igloo Programvaruklienten supportteamet](https://www.igloosoftware.com/services/support) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. På den **Igloo programvarukonfiguration** klickar du på **konfigurera Igloo programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen för företagets Igloo programvara som en administratör.

8. Gå till den **Kontrollpanelen**.
   
     ![Kontrollpanelen](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "Kontrollpanelen")

9. I den **medlemskap** klickar du på **logga inställningarna**.
   
    ![Logga in inställningar](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "logga in inställningarna")

10. Klicka på i konfigurationsavsnittet SAML **konfigurera SAML-autentisering**.
   
    ![SAML-konfiguration](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "SAML-konfiguration")
   
11. I den **Allmän konfiguration** avsnittet, utför följande steg:
   
    ![Allmän konfiguration](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "Allmän konfiguration")

    a. I den **anslutningsnamn** textruta skriver du ett eget namn för din konfiguration.
   
    b. I den **IdP inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    c. I den **IdP logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
    
    d. Välj **logga ut svar och typ av begäran HTTP** som **efter**.
   
    e. Öppna din **Base64-** kodade certifikatet i anteckningar hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat med offentlig** textruta.
    
12. I den **svar och Autentiseringskonfiguration**, utför följande steg:
    
    ![Svar och Autentiseringskonfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "svar och Autentiseringskonfiguration")
  
      a. Som **identitetsleverantör**väljer **Microsoft ADFS**.
      
      b. Som **Ämnesidentifierarens typ**väljer **e-postadress**. 

      c. I den **e-attributet** textruta typen **e-postadress**.

      d. I den **förnamn attributet** textruta typen **givenname**.

      e. I den **senaste namnattributet** textruta typen **efternamn**.

13. Utför följande steg för att slutföra konfigurationen:
    
    ![Skapa användare på inloggning](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "skapa användare på Logga in") 

     a. Som **skapa användare på inloggning**väljer **skapa en ny användare på din plats när de loggar in**.

     b. Som **inloggning inställningar**väljer **Använd SAML-knappen ”Logga in” skärmen**.

     c. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-igloo-software-test-user"></a>Skapa en testanvändare Igloo programvara

Det finns inga uppgiften att konfigurera användaretablering Igloo programvara.  

När en tilldelad användare försöker logga in på Igloo programvara med hjälp av åtkomstpanelen kontrollerar Igloo programvara om användaren finns.  Om det finns inget användarkonto ännu, skapas den automatiskt med Igloo program.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Igloo programvara.

![Tilldela användare][200] 

**Om du vill tilldela Igloo programvara Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Igloo programvara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Igloo programvara på åtkomstpanelen du bör få automatiskt loggat in på Igloo programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png

