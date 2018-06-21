---
title: 'Självstudier: Azure Active Directory-integrering med BenefitHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BenefitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4069fe32-a452-463f-973e-7aa0baa4c2fa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 5accb676c7fb732c9b59dbbbeda944a3a3015d50
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285424"
---
# <a name="tutorial-azure-active-directory-integration-with-benefithub"></a>Självstudier: Azure Active Directory-integrering med BenefitHub

I kursen får lära du att integrera BenefitHub med Azure Active Directory (AD Azure).

Integrera BenefitHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BenefitHub
- Du kan aktivera användarna att automatiskt hämta loggat in på BenefitHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med BenefitHub, behöver du följande:

- En Azure AD-prenumeration
- En BenefitHub enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till BenefitHub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-benefithub-from-the-gallery"></a>Att lägga till BenefitHub från galleriet
Du måste lägga till BenefitHub från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av BenefitHub i Azure AD.

**Utför följande steg för att lägga till BenefitHub från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **BenefitHub**.

    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/tutorial_benefithub_search.png)

5. Välj i resultatpanelen **BenefitHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/tutorial_benefithub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med BenefitHub baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i BenefitHub motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i BenefitHub upprättas.

I BenefitHub, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med BenefitHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare BenefitHub](#creating-a-benefithub-test-user)**  – du har en motsvarighet för Britta Simon i BenefitHub som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BenefitHub program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BenefitHub:**

1. I Azure-portalen på den **BenefitHub** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_benefithub_samlbase.png)

3. På den **BenefitHub domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_benefithub_url1.png)
  
    a. I den **identifierare** textruta typ: `urn:benefithub:passport`
    
    b. I den **Reply URL** textruta typ: `https://passport.benefithub.info/saml/post/ac`

4. Programmet BenefitHub förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. 

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_benefithub_attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | organisations-ID | organisations-< ID > |

    > [!NOTE]
    > Det här attributvärdet är inte verkliga. Uppdatera det här värdet med faktiska organisations-ID. Kontakta [BenefitHub supportteamet](https://www.benefithub.com/Home/ContactUs) att hämta den faktiska organisations-ID.
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-kontroll måste du kontakta din [BenefitHub stöd](https://www.benefithub.com/Home/ContactUs) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program.

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_benefithub_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_general_400.png)

8. Konfigurera enkel inloggning på **BenefitHub** sida, måste du skicka den hämtade **XML-Metadata för** till [BenefitHub supportteamet](https://www.benefithub.com/Home/ContactUs). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/benefithub-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-benefithub-test-user"></a>Skapa en testanvändare BenefitHub

I det här avsnittet skapar du en användare som kallas Britta Simon i BenefitHub. Arbeta med [BenefitHub supportteamet](https://www.benefithub.com/Home/ContactUs) att lägga till användare i BenefitHub-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BenefitHub.

![Tilldela användare][200] 

**Om du vill tilldela BenefitHub Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **BenefitHub**.

    ![Konfigurera enkel inloggning](./media/benefithub-tutorial/tutorial_benefithub_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen BenefitHub på åtkomstpanelen du bör få automatiskt loggat in på ditt BenefitHub program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/benefithub-tutorial/tutorial_general_01.png
[2]: ./media/benefithub-tutorial/tutorial_general_02.png
[3]: ./media/benefithub-tutorial/tutorial_general_03.png
[4]: ./media/benefithub-tutorial/tutorial_general_04.png

[100]: ./media/benefithub-tutorial/tutorial_general_100.png

[200]: ./media/benefithub-tutorial/tutorial_general_200.png
[201]: ./media/benefithub-tutorial/tutorial_general_201.png
[202]: ./media/benefithub-tutorial/tutorial_general_202.png
[203]: ./media/benefithub-tutorial/tutorial_general_203.png
