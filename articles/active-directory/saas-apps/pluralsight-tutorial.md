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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 81f7e6f7610eb855bd4df1eaf45c6d016befc133
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443622"
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Självstudier: Azure Active Directory-integrering med Pluralsight

I den här självstudien får du lära dig hur du integrerar Pluralsight med Azure Active Directory (AD Azure).

Integrera Pluralsight med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pluralsight.
- Du kan aktivera användarna att automatiskt få loggat in på Pluralsight (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pluralsight, behöver du följande objekt:

- En Azure AD-prenumeration
- En enda inloggning aktiverad prenumeration på Pluralsight

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Pluralsight från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pluralsight-from-the-gallery"></a>Att lägga till Pluralsight från galleriet
För att konfigurera integrering av Pluralsight i Azure AD, som du behöver lägga till Pluralsight från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pluralsight från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Pluralsight**väljer **Pluralsight** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Pluralsight i resultatlistan](./media/pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pluralsight baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Pluralsight är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Pluralsight upprättas.

I Pluralsight, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pluralsight, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Pluralsight](#create-a-pluralsight-test-user)**  – du har en motsvarighet för Britta Simon i Pluralsight som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med Pluralsight.

**Om du vill konfigurera Azure AD enkel inloggning med Pluralsight, utför du följande steg:**

1. I Azure-portalen på den **Pluralsight** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

1. På den **Pluralsight domän och URL: er** avsnittet, utför följande steg:

    ![Pluralsight-domän och URL: er med enkel inloggning för information](./media/pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<instancename>.pluralsight.com/sso/<companyname>`

    b. I den **identifierare** textrutan anger du URL: `www.pluralsight.com`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<instancename>.pluralsight.com/sp/ACS.saml2`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL. Kontakta [Pluralsight klienten supportteamet](mailto:support@pluralsight.com) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

1. Målet med det här avsnittet är att aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Pluralsight-programmet.

    Pluralsight-program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >Du kan också lägga till den **”unikt ID”** attributet med lämpligt värde som EmployeeID eller något annat som passar för din organisation. Observera också att detta inte är det obligatoriska attributet; men du kan lägga till den för att identifiera unika användare. 

1. Att lägga till de nödvändiga **SAML-tokenattribut**, utför följande steg för varje rad som visas i tabellen nedan:
   
   | Attributnamn | Attributvärde |
   | ---| --- |
   | Förnamn |User.givenName |
   | Efternamn |User.surname |
   | E-post |User.Mail |
   
   a. Klicka på **lägga till användarattribut** att öppna den **lägga till användarattribut** dialogrutan.
    
     ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. I den **attributnamnet** textrutan skriver du attributnamnet som visas för den raden.
  
   c. Från den **attributvärdet** väljer attribut-värde som visas för den raden.
  
   d. Klicka på **OK**.    

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/pluralsight-tutorial/tutorial_general_400.png)

1. För att få SSO konfigurerats för ditt program kan kontakta [Pluralsight professionella tjänster](mailTo:professionalservices@pluralsight.com) team och ange hämtade metadatafilen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/pluralsight-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/pluralsight-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/pluralsight-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/pluralsight-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pluralsight-test-user"></a>Skapa en testanvändare för Pluralsight

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pluralsight. Kontakta [Pluralsight klienten supportteamet](mailto:support@pluralsight.com) att lägga till användare i Pluralsight-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pluralsight.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Pluralsight, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Pluralsight**.

    ![Pluralsight-länk i listan med program](./media/pluralsight-tutorial/tutorial_pluralsight_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pluralsight i åtkomstpanelen du bör få automatiskt loggat in på ditt program med Pluralsight.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

