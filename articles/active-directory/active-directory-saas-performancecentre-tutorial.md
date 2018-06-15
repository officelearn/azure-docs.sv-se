---
title: 'Självstudier: Azure Active Directory-integrering med PerformanceCentre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: afee73a18502c48a6ff933f269f2572333d14196
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349234"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Självstudier: Azure Active Directory-integrering med PerformanceCentre

I kursen får lära du att integrera PerformanceCentre med Azure Active Directory (AD Azure).

Integrera PerformanceCentre med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PerformanceCentre
- Du kan aktivera användarna att automatiskt hämta loggat in på PerformanceCentre (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med PerformanceCentre, behöver du följande:

- En Azure AD-prenumeration
- En PerformanceCentre enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till PerformanceCentre från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-performancecentre-from-the-gallery"></a>Att lägga till PerformanceCentre från galleriet
Du måste lägga till PerformanceCentre från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av PerformanceCentre i Azure AD.

**Utför följande steg för att lägga till PerformanceCentre från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **PerformanceCentre**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. Välj i resultatpanelen **PerformanceCentre**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med PerformanceCentre baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i PerformanceCentre motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PerformanceCentre upprättas.

I PerformanceCentre, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med PerformanceCentre, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare PerformanceCentre](#creating-a-performancecentre-test-user)**  – du har en motsvarighet för Britta Simon i PerformanceCentre som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PerformanceCentre program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PerformanceCentre:**

1. I Azure-portalen på den **PerformanceCentre** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. På den **PerformanceCentre domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `http://companyname.performancecentre.com/saml/SSO`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [PerformanceCentre klienten supportteamet](https://www.performancecentre.com/contact-us/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. På den **PerformanceCentre Configuration** klickar du på **konfigurera PerformanceCentre** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Logga in på ditt **PerformanceCentre** företagets webbplats som administratör.

8. Klicka på fliken på vänster sida **konfigurera**.
   
    ![Azure AD-Single Sign-On][10]

9. Klicka på fliken på vänster sida **diverse**, och klicka sedan på **enkel inloggning**.
   
    ![Azure AD-Single Sign-On][11]

10. Som **protokollet**väljer **SAML**.
   
    ![Azure AD-Single Sign-On][12]

11. Öppna hämtade metadatafilen i anteckningar, kopiera innehållet, klistrar in det i den **identitet providern Metadata** textruta och klicka sedan på **spara**.
   
    ![Azure AD-Single Sign-On][13]

12. Kontrollera att värdena för den **entitet bas-URL** och **entitet ID URL** är korrekta.
    
     ![Azure AD-Single Sign-On][14]

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-performancecentre-test-user"></a>Skapa en testanvändare PerformanceCentre

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i PerformanceCentre.

**Utför följande steg för att skapa en användare som kallas Britta Simon i PerformanceCentre:**

1. Logga in på webbplatsen PerformanceCentre företag som administratör.

2. Klicka på menyn till vänster **Interrelate**, och klicka sedan på **skapa deltagare**.
   
    ![Skapa användare][400]

3. På den **samband - skapa deltagare** dialogrutan, utför följande steg:
   
    ![Skapa användare][401]
    
    a. Ange attribut som krävs för Britta Simon till relaterade textrutor.
    
    >[!IMPORTANT]
    >Britta's User Name-attribut i PerformanceCentre måste vara samma som användarnamnet i Azure AD.
    
    b. Välj **administratör** som **Välj rollen**.
    
    c. Klicka på **Spara**. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PerformanceCentre.

![Tilldela användare][200] 

**Om du vill tilldela PerformanceCentre Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **PerformanceCentre**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen PerformanceCentre på åtkomstpanelen du bör få automatiskt loggat in på ditt PerformanceCentre program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png

