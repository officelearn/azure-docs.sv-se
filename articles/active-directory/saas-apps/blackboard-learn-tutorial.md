---
title: 'Självstudier: Azure Active Directory-integration med svart tavla Läs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och lär dig svart tavla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jeedes
ms.openlocfilehash: 1148e6f85905888eccafde638de66215073e0eaa
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043694"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Självstudier: Azure Active Directory-integration med svart tavla Läs

I den här självstudien får du lära dig hur du integrerar svart tavla Lär dig med Azure Active Directory (AD Azure).

Integrera svart tavla Lär dig med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till svart tavla Läs
- Du kan aktivera användarna att automatiskt få loggat in på svart tavla Läs (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med svart tavla Lär dig, behöver du följande objekt:

- En Azure AD-prenumeration
- En svart tavla Läs enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till svart tavla Läs från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-blackboard-learn-from-the-gallery"></a>Att lägga till svart tavla Läs från galleriet
För att konfigurera integrering av svart tavla Lär dig i Azure AD, som du behöver lägga till svart tavla Läs från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till svart tavla Läs från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **svart tavla Läs**.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_search.png)

5. I resultatpanelen väljer **svart tavla Läs**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med svart tavla Läs baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i svart tavla lära dig är att en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i svart tavla Läs upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i svart tavla Läs.

Om du vill konfigurera och testa Azure AD enkel inloggning med svart tavla Läs, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en svart tavla Läs testanvändare](#creating-a-blackboard-learn-test-user)**  – du har en motsvarighet för Britta Simon i svart tavla Läs som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt svart tavla Läs program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med svart tavla Läs:**

1. I Azure-portalen på den **svart tavla Läs** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_samlbase.png)

3. På den **svart tavla Läs domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.blackboard.com/`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [svart tavla Läs klienten supportteamet](https://www.blackboard.com/support/index.aspx) att hämta dessa värden. 

4. Svart tavla Läs program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet.
 Följande skärmbild visar ett exempel på hur den.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_attribute.png)

5. I den **användarattribut** avsnittet på **enkel inloggning** dialogrutan Konfigurera SAML-tokenattribut som visas i bilden och utför följande steg. Vi har mappat Userprincipalname som det unika användarattributet här men kan du mappa den till lämpligt värde som särskiljer unikt användare i organisationen och som mappar till svart tavla Läs användarnamnfältet.
           
    | Attributnamn | Attributvärde |   
    | ---------------| ----------------|
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |User.userPrincipalName |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_certificate.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_general_400.png)

8. På den **svart tavla Läs Configuration** klickar du på **konfigurera svart tavla Läs** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_configure.png) 

9. Att konfigurera enkel inloggning på **svart tavla Läs** sida, som du behöver skicka de hämtade **XML-Metadata för** och **SAML entitets-ID** till [svart tavla Läs stöd för](https://www.blackboard.com/support/index.aspx).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-blackboard-learn-test-user"></a>Skapa en svart tavla Läs testanvändare
I det här avsnittet skapar du en användare som kallas Britta Simon i svart tavla Läs. 

Svart tavla Läs program stöder just-in-time användaretablering. Se till att du har konfigurerat anspråk enligt beskrivningen i avsnittet  **[konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till svart tavla Läs.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon svart tavla mer, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **svart tavla Läs**.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen svart tavla Läs i åtkomstpanelen du bör få automatiskt loggat in på ditt svart tavla Läs program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-tutorial/tutorial_general_203.png

