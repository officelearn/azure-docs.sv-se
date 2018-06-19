---
title: 'Självstudier: Azure Active Directory-integrering med önster Karlsson Factiva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och önster Karlsson Factiva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b36e97e8-37a6-4096-a894-530427ee1331
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2017
ms.author: jeedes
ms.openlocfilehash: 602824114c025ff91e97b1b29e14f4c424415c55
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966559"
---
# <a name="tutorial-azure-active-directory-integration-with-dow-jones-factiva"></a>Självstudier: Azure Active Directory-integrering med önster Karlsson Factiva

I kursen får lära du att integrera önster Karlsson Factiva med Azure Active Directory (AD Azure).

Integrera önster Karlsson Factiva med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till önster Karlsson Factiva
- Du kan aktivera användarna att automatiskt hämta loggat in på önster Karlsson Factiva (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med önster Karlsson Factiva, behöver du följande:

- En Azure AD-prenumeration
- En önster Karlsson Factiva enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till önster Karlsson Factiva från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dow-jones-factiva-from-the-gallery"></a>Att lägga till önster Karlsson Factiva från galleriet
Du måste lägga till önster Karlsson Factiva från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av önster Karlsson Factiva i Azure AD.

**Utför följande steg för att lägga till önster Karlsson Factiva från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **önster Karlsson Factiva**.

    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_search.png)

5. Välj i resultatpanelen **önster Karlsson Factiva**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med önster Karlsson Factiva baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i önster Karlsson Factiva motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i önster Karlsson Factiva upprättas.

I önster Karlsson Factiva tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med önster Karlsson Factiva, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare önster Karlsson Factiva](#creating-a-dow-jones-factiva-test-user)**  – har en motsvarighet för Britta Simon önster Karlsson Factiva som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet önster Karlsson Factiva.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med önster Karlsson Factiva:**

1. I Azure-portalen på den **önster Karlsson Factiva** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_samlbase.png)

3. På den **önster Karlsson Factiva domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![Konfigurera enkel inloggning](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/dowjones-factiva-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **önster Karlsson Factiva** sida, måste du skicka den hämtade **XML-Metadata för** till [önster Karlsson Factiva supportteamet](https://www.dowjones.com/contact/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/dowjones-factiva-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-dow-jones-factiva-test-user"></a>Skapa en testanvändare önster Karlsson Factiva

I det här avsnittet skapar du en användare som kallas Britta Simon i önster Karlsson Factiva. Se tillsammans med önster [Karlsson Factiva supportteamet](https://www.dowjones.com/contact/) att lägga till användare i önster Karlsson Factiva-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till önster Karlsson Factiva.

![Tilldela användare][200] 

**Om du vill tilldela önster Karlsson Factiva Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **önster Karlsson Factiva**.

    ![Konfigurera enkel inloggning](./media/dowjones-factiva-tutorial/tutorial_dowjonesfactiva_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen önster Karlsson Factiva på åtkomstpanelen du bör få automatiskt loggat in på ditt önster Karlsson Factiva program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dowjones-factiva-tutorial/tutorial_general_01.png
[2]: ./media/dowjones-factiva-tutorial/tutorial_general_02.png
[3]: ./media/dowjones-factiva-tutorial/tutorial_general_03.png
[4]: ./media/dowjones-factiva-tutorial/tutorial_general_04.png

[100]: ./media/dowjones-factiva-tutorial/tutorial_general_100.png

[200]: ./media/dowjones-factiva-tutorial/tutorial_general_200.png
[201]: ./media/dowjones-factiva-tutorial/tutorial_general_201.png
[202]: ./media/dowjones-factiva-tutorial/tutorial_general_202.png
[203]: ./media/dowjones-factiva-tutorial/tutorial_general_203.png

