---
title: 'Självstudier: Azure Active Directory-integrering med LogicMonitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 8812d2794c08d2ab7a50f07835cda77c159471c2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352964"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudier: Azure Active Directory-integrering med LogicMonitor

I kursen får lära du att integrera LogicMonitor med Azure Active Directory (AD Azure).

Integrera LogicMonitor med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LogicMonitor
- Du kan aktivera användarna att automatiskt hämta loggat in på LogicMonitor (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med LogicMonitor, behöver du följande:

- En Azure AD-prenumeration
- En LogicMonitor enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till LogicMonitor från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-logicmonitor-from-the-gallery"></a>Att lägga till LogicMonitor från galleriet
Du måste lägga till LogicMonitor från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LogicMonitor i Azure AD.

**Utför följande steg för att lägga till LogicMonitor från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LogicMonitor**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_search.png)

5. Välj i resultatpanelen **LogicMonitor**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LogicMonitor baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LogicMonitor motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LogicMonitor upprättas.

I LogicMonitor, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med LogicMonitor, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LogicMonitor](#creating-a-logicmonitor-test-user)**  – du har en motsvarighet för Britta Simon i LogicMonitor som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LogicMonitor program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LogicMonitor:**

1. I Azure-portalen på den **LogicMonitor** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

3. På den **LogicMonitor domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.logicmonitor.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [LogicMonitor klienten supportteamet](https://www.logicmonitor.com/contact/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_400.png)

6. Logga in på ditt **LogicMonitor** företagets webbplats som administratör.

7. Klicka på menyn högst upp **inställningar**.
   
   ![Inställningar för](./media/active-directory-saas-logicmonitor-tutorial/ic790052.png "inställningar")

8. I navigeringsfönstret bat på vänster sida, klickar du på **enkel inloggning**
   
   ![Enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/ic790053.png "enkel inloggning")

9. I den **inställningar för enkel inloggning (SSO)** avsnittet, utför följande steg:
   
   ![Enkel inloggning inställningar](./media/active-directory-saas-logicmonitor-tutorial/ic790054.png "enkel inloggning inställningar")
   
   a. Välj **aktivera enkel inloggning**.

   b. Som **standard rolltilldelning**väljer **readonly**.
   
   c. Öppna metadatafilen hämtade i anteckningar och klistra in innehållet i filen till den **identitet providern Metadata** textruta.
   
   d. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Skapa en testanvändare LogicMonitor

För AAD-användare för att kunna logga in, måste de etableras till LogicMonitor programmet med hjälp av deras användarnamn för Azure Active Directory.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på webbplatsen LogicMonitor företag som administratör.

2. Klicka på menyn högst upp **inställningar**, och klicka sedan på **roller och användare**.
   
   ![Roller och användare](./media/active-directory-saas-logicmonitor-tutorial/ic790056.png "roller och användare")

3. Klicka på **Lägg till**.

4. I den **Lägg till ett konto** avsnittet, utför följande steg:
   
   ![Lägg till ett konto](./media/active-directory-saas-logicmonitor-tutorial/ic790057.png "Lägg till ett konto")
   
   a. Typ av **användarnamn**, **e-post**, **lösenord**, och **ange lösenord** värden för Azure Active Directory-användare som du vill etablera i relaterade textrutor.
   
   b. Välj **roller**, **behörighet att visa**, och **Status**.
   
   c. Klicka på **skicka**.

>[!NOTE]
>Du kan använda något annat LogicMonitor användarens konto skapas verktyg eller API: er som tillhandahålls av LogicMonitor för att etablera Azure Active Directory användarkonton. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LogicMonitor.

![Tilldela användare][200] 

**Om du vill tilldela LogicMonitor Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **LogicMonitor**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
 
När du klickar på panelen LogicMonitor på åtkomstpanelen du bör få automatiskt loggat in på ditt LogicMonitor program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_203.png

