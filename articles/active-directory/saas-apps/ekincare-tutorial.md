---
title: 'Självstudier: Azure Active Directory-integrering med eKincare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och eKincare.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 57f56d14-83cf-4cbb-b342-fac4fc60078f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 413ae4197b95932321e29eb1662fe8168c77799a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216947"
---
# <a name="tutorial-azure-active-directory-integration-with-ekincare"></a>Självstudier: Azure Active Directory-integrering med eKincare

I kursen får lära du att integrera eKincare med Azure Active Directory (AD Azure).

Integrera eKincare med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till eKincare
- Du kan aktivera användarna att automatiskt hämta loggat in på eKincare (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med eKincare, behöver du följande:

- En Azure AD-prenumeration
- En eKincare enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till eKincare från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ekincare-from-the-gallery"></a>Att lägga till eKincare från galleriet
Du måste lägga till eKincare från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av eKincare i Azure AD.

**Utför följande steg för att lägga till eKincare från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **eKincare**.

    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/tutorial_ekincare_search.png)

5. Välj i resultatpanelen **eKincare**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/tutorial_ekincare_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med eKincare baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i eKincare motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i eKincare upprättas.

I eKincare, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med eKincare, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare eKincare](#creating-a-ekincare-test-user)**  – du har en motsvarighet för Britta Simon i eKincare som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt eKincare program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med eKincare:**

1. I Azure-portalen på den **eKincare** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/tutorial_ekincare_samlbase.png)

3. På den **eKincare domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/tutorial_ekincare_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<instancename>.ekincare.com/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<instancename>.ekincare.com/hul/saml`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [eKincare supportteamet](mailto:tech@ekincare.com) att hämta dessa värden.
 
4. eKincare program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel på den här konfigurationen.

    Anspråkets namn kommer alltid att **”employeeid”** och värdet som vi har mappats till user.extensionattribute1 som innehåller employeeid för användaren. De andra två anspråk name engångsfaktorautentisering **”organisations-ID”** och **”organisationsnamn”** är alltid samma och deras värden innehåller information om organisationen för användaren.
    
    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/attribute.png)
    
5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | EmployeeID | *User.extensionattribute1* |
    | organisations-ID | *”uniquevalue”* |
    | organisationsnamn | *User.CompanyName* |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/04.png)

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/tutorial_ekincare_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/tutorial_general_400.png)

8. Konfigurera enkel inloggning på **eKincare** sida, måste du skicka den hämtade **XML-Metadata för** till [eKincare supportteamet](mailto:tech@ekincare.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/ekincare-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-ekincare-test-user"></a>Skapa en testanvändare eKincare

Programmet stöder bara i tid användaretablering och authentication-användare skapas automatiskt i programmet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till eKincare.

![Tilldela användare][200] 

**Om du vill tilldela eKincare Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **eKincare**.

    ![Konfigurera enkel inloggning](./media/ekincare-tutorial/tutorial_ekincare_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen eKincare på åtkomstpanelen du bör få automatiskt loggat in på ditt eKincare program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ekincare-tutorial/tutorial_general_01.png
[2]: ./media/ekincare-tutorial/tutorial_general_02.png
[3]: ./media/ekincare-tutorial/tutorial_general_03.png
[4]: ./media/ekincare-tutorial/tutorial_general_04.png

[100]: ./media/ekincare-tutorial/tutorial_general_100.png

[200]: ./media/ekincare-tutorial/tutorial_general_200.png
[201]: ./media/ekincare-tutorial/tutorial_general_201.png
[202]: ./media/ekincare-tutorial/tutorial_general_202.png
[203]: ./media/ekincare-tutorial/tutorial_general_203.png

