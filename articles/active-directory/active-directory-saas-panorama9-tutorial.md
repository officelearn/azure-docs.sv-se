---
title: "Självstudier: Azure Active Directory-integrering med Panorama9 | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Panorama9."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 7cbb6e45f730b8eaef4cb25307cfeaf9f1c853a1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Självstudier: Azure Active Directory-integrering med Panorama9

I kursen får lära du att integrera Panorama9 med Azure Active Directory (AD Azure).

Integrera Panorama9 med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Panorama9
- Du kan aktivera användarna att automatiskt hämta loggat in på Panorama9 (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Panorama9, behöver du följande:

- En Azure AD-prenumeration
- En Panorama9 enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Panorama9 från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-panorama9-from-the-gallery"></a>Att lägga till Panorama9 från galleriet
Du måste lägga till Panorama9 från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Panorama9 i Azure AD.

**Utför följande steg för att lägga till Panorama9 från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Panorama9**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_search.png)

5. Välj i resultatpanelen **Panorama9**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Panorama9 baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Panorama9 motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Panorama9 upprättas.

I Panorama9, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Panorama9, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Panorama9](#creating-a-panorama9-test-user)**  – du har en motsvarighet för Britta Simon i Panorama9 som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Panorama9 program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Panorama9:**

1. I Azure-portalen på den **Panorama9** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. På den **Panorama9 domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL som:`https://dashboard.panorama9.com/saml/access/3262`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Panorama9 klienten supportteamet](https://support.panorama9.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_general_400.png)

6. På den **Panorama9 Configuration** klickar du på **konfigurera Panorama9** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_configure.png) 

5. Logga in på webbplatsen Panorama9 företag som en administratör i en annan webbläsarfönster.

6. Klicka på i verktygsfältet högst upp **hantera**, och klicka sedan på **tillägg**.
   
   ![Tillägg](./media/active-directory-saas-panorama9-tutorial/ic790023.png "tillägg")
7. På den **tillägg** dialogrutan klickar du på **enkel inloggning**.
   
   ![Enkel inloggning](./media/active-directory-saas-panorama9-tutorial/ic790024.png "enkel inloggning")
8. I den **inställningar** avsnittet, utför följande steg:
   
   ![Inställningar för](./media/active-directory-saas-panorama9-tutorial/ic790025.png "inställningar")
   
    a. I **identitet providern URL** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
   
    b. I **certifikat fingeravtryck** textruta klistra in den **tumavtrycket** värdet för certifikat som du har kopierat från Azure-portalen.    
         
9. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-panorama9-test-user"></a>Skapa en testanvändare Panorama9

För att aktivera Azure AD-användare att logga in på Panorama9 etableras de i Panorama9.  

När det gäller Panorama9 är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Panorama9** företagets webbplats som administratör.

2. Klicka på menyn högst upp **hantera**, och klicka sedan på **användare**.
   
  ![Användare](./media/active-directory-saas-panorama9-tutorial/ic790027.png "användare")

3. I avsnittet användare klickar du på  **+**  att lägga till nya användare.

 ![Användare](./media/active-directory-saas-panorama9-tutorial/ic790028.png "användare")

4. Gå till avsnittet användaren data, ange en giltig Azure Active Directory-användare som du vill etablera i e-postadress i **e-post** textruta.

5. Gå till avsnittet för användare klickar på **spara**.
   
> [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Panorama9.

![Tilldela användare][200] 

**Om du vill tilldela Panorama9 Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Panorama9**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Panorama9 på åtkomstpanelen du bör få automatiskt loggat in på Panorama9 program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_203.png

