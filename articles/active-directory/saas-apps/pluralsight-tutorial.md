---
title: 'Självstudier: Azure Active Directory-integrering med Pluralsight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pluralsight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 862f41c74b289bdf80746ed785883315fd08c3ad
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964038"
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Självstudier: Azure Active Directory-integrering med Pluralsight

I kursen får lära du att integrera Pluralsight med Azure Active Directory (AD Azure).

Integrera Pluralsight med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pluralsight.
- Du kan aktivera användarna att automatiskt hämta loggat in på Pluralsight (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Pluralsight, behöver du följande:

- En Azure AD-prenumeration
- En Pluralsight enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Pluralsight från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pluralsight-from-the-gallery"></a>Att lägga till Pluralsight från galleriet
Du måste lägga till Pluralsight från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Pluralsight i Azure AD.

**Utför följande steg för att lägga till Pluralsight från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Pluralsight**väljer **Pluralsight** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Pluralsight i resultatlistan](./media/pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Pluralsight baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Pluralsight motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Pluralsight upprättas.

I Pluralsight, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pluralsight, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Pluralsight](#create-a-pluralsight-test-user)**  – du har en motsvarighet för Britta Simon i Pluralsight som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Pluralsight program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pluralsight:**

1. I Azure-portalen på den **Pluralsight** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

3. På den **Pluralsight domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Pluralsight domän med enkel inloggning information](./media/pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<instancename>.pluralsight.com/sso/<companyname>`

    b. I den **identifierare** textruta anger du URL: `www.pluralsight.com`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<instancename>.pluralsight.com/sp/ACS.saml2`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Pluralsight klienten supportteamet](mailto:support@pluralsight.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

5. Syftet med det här avsnittet är att aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Pluralsight-programmet.

    Programmet Pluralsight förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >Du kan också lägga till den **”unikt ID”** attribut med lämpligt värde som EmployeeID eller något annat som passar för din organisation. Observera att detta inte är det obligatoriska attributet; Du kan dock lägga till den för att identifiera unika användare. 

6. Att lägga till de nödvändiga **SAML-token attribut**, utför följande steg för varje rad som visas i tabellen nedan:
   
   | Attributnamn | Attributvärde |
   | ---| --- |
   | Förnamn |User.givenName |
   | Efternamn |User.surname |
   | E-post |User.Mail |
   
   a. Klicka på **lägga till användarattribut** att öppna den **lägga till användarattribut** dialogrutan.
    
     ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. I den **attributnamn** textruta ange attributets namn visas för den raden.
  
   c. Från den **attributvärdet** väljer du det attributvärde som visas för den raden.
  
   d. Klicka på **OK**.    

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_general_400.png)

8. För att få SSO konfigurerats för ditt program, kontakta [Pluralsight professionella tjänster](mailTo:professionalservices@pluralsight.com) grupp- och ange den hämtade metadata.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/pluralsight-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/pluralsight-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/pluralsight-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/pluralsight-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pluralsight-test-user"></a>Skapa en testanvändare Pluralsight

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pluralsight. Se tillsammans med [Pluralsight klienten supportteamet](mailto:support@pluralsight.com) att lägga till användare i Pluralsight-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pluralsight.

![Tilldela rollen][200] 

**Om du vill tilldela Pluralsight Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Pluralsight**.

    ![Länken Pluralsight i listan med program](./media/pluralsight-tutorial/tutorial_pluralsight_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Pluralsight på åtkomstpanelen du bör få automatiskt loggat in på ditt Pluralsight program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/pluralsight-tutorial/tutorial_general_203.png

