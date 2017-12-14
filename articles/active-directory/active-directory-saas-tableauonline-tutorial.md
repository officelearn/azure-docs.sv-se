---
title: "Självstudier: Azure Active Directory-integrering med Tableau Online | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 47ae9dbde509726065da7eaee2c7aec491389f45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Självstudier: Azure Active Directory-integrering med Tableau Online

Lär dig hur du integrerar Tableau Online med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera Tableau Online med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Online
- Du kan aktivera användarna att automatiskt hämta loggat in på Tableau Online (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Tableau Online, behöver du följande:

- En Azure AD-prenumeration
- En Tableau Online enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Tableau Online från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-tableau-online-from-the-gallery"></a>Att lägga till Tableau Online från galleriet
Du måste lägga till Tableau Online från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Tableau Online i Azure AD.

**Om du vill lägga till Tableau Online från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Tableau Online**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. Välj i resultatpanelen **Tableau Online**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Tableau Online baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Tableau Online till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Tableau Online upprättas.

I Tableau Online, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Online, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Tableau Online testanvändare](#creating-a-tableau-online-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Online som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Tableau Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Online:**

1. I Azure-portalen på den **Tableau Online** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. På den **Tableau Online domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. I den **inloggnings-URL** textruta anger du URL:`https://sso.online.tableau.com`

    b. I den **identifierare** textruta anger du URL:`https://sso.online.tableau.com/public/sp/<instancename>`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster inloggning i tillämpningsprogrammet Tableau Online. Gå till **inställningar** och sedan **autentisering**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Aktivera SAML Under **autentiseringstyper** avsnitt. Kontrollera den **enkel inloggning med SAML** kryssrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Rulla nedåt tills **metadata importfilen till Tableau Online** avsnitt.  Klicka på Bläddra och importera metadatafilen som du har hämtat från Azure AD. Klicka på **tillämpa**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. I den **matchar intyg** avsnittet, infoga motsvarande identitetsleverantör assertion namn för **e-postadress**, **Förnamn**, och **efternamn**. Hämta informationen från Azure AD: 
  
    a. I Azure-portalen går du den **Tableau Online** sidan för integrering av programmet.
    
    b. I attributavsnittet väljer den **”visa och redigera andra användarattribut”** kryssrutan. 
    
   ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Kopiera namnområdesvärdet för dessa attribut: givenname, e-post- och efternamn med hjälp av följande steg:

   ![Azure AD-Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Klicka på **user.givenname** värde 
    
    e. Kopiera värdet från den **namnområde** textruta.

   ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Om du vill kopiera namesapce anvisningarna värden för efternamn och e-ovan.

    g. Växla till programmet Tableau Online, och sedan ange den **Tableau Onlineattribut** avsnittet på följande sätt:
     * E-post: **e** eller **userprincipalname**
     * Förnamn: **givenname**
     * Efternamn: **efternamn**
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-tableau-online-test-user"></a>Skapa en Tableau Online testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Tableau Online.

1. På **Tableau Online**, klickar du på **inställningar** och sedan **autentisering** avsnitt. Rulla ned till **Välj användare** avsnitt. Klicka på **lägga till användare** och sedan **ange e-postadresser**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Välj **lägga till användare för enkel inloggning (SSO) autentisering**. I den **ange e-postadresser** textruta Lägg tillbritta.simon@contoso.com
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Klicka på **Skapa**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Online.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Tableau Online, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Tableau Online**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Tableau Online på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet Tableau Online.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png

