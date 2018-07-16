---
title: 'Självstudier: Azure Active Directory-integration med 15Five | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 15Five.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 3cc0d9122fd7335bc29c7f35c1163cb39d3481b8
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054309"
---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>Självstudier: Azure Active Directory-integration med 15Five

I den här självstudien får du lära dig hur du integrerar 15Five med Azure Active Directory (AD Azure).

Integrera 15Five med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till 15Five
- Du kan aktivera användarna att automatiskt få loggat in på 15Five (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med 15Five, behöver du följande objekt:

- En Azure AD-prenumeration
- En 15Five enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till 15Five från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-15five-from-the-gallery"></a>Att lägga till 15Five från galleriet
För att konfigurera integrering av 15Five i Azure AD, som du behöver lägga till 15Five från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 15Five från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **15Five**.

    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/tutorial_15five_search.png)

5. I resultatpanelen väljer **15Five**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/tutorial_15five_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med 15Five baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i 15Five är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i 15Five upprättas.

I 15Five, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med 15Five, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare 15Five](#creating-a-15five-test-user)**  – du har en motsvarighet för Britta Simon i 15Five som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt 15Five program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med 15Five:**

1. I Azure-portalen på den **15Five** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/15five-tutorial/tutorial_15five_samlbase.png)

3. På den **15Five domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/15five-tutorial/tutorial_15five_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.15five.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.15five.com/saml2/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [15Five klienten supportteamet](https://www.15five.com/contact/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/15five-tutorial/tutorial_15five_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/15five-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **15Five** sida, som du behöver skicka de hämtade **XML-Metadata för** till [15Five supportteamet](https://www.15five.com/contact/).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/15five-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-15five-test-user"></a>Skapa en 15Five testanvändare

Om du vill aktivera Azure AD-användare att logga in på 15Five, måste de etableras i 15Five. När 15Five, etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
1. Logga in på din **15Five** företagets plats som administratör.

2. Gå till **hantera företagets**.
   
    ![Hantera företagets](./media/15five-tutorial/IC784675.png "hantera företagets")

3. Gå till **personer \> Lägg till personer**.
   
    ![Personer](./media/15five-tutorial/IC784676.png "personer")

4. Utför följande steg i avsnittet Lägg till ny Person:
   
    ![Lägg till ny Person](./media/15five-tutorial/IC784677.png "Lägg till ny Person")
   
    a. Skriv den **Förnamn**, **efternamn**, **rubrik**, **e-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i den relaterade textrutor.

    b. Klicka på **Klar**.
   
    > [!NOTE]
    > Azure AD-kontoinnehavare tar emot ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.
   
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 15Five.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon 15Five, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **15Five**.

    ![Konfigurera enkel inloggning](./media/15five-tutorial/tutorial_15five_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen 15Five i åtkomstpanelen, bör du få inloggningssidan i 15Five program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/15five-tutorial/tutorial_general_01.png
[2]: ./media/15five-tutorial/tutorial_general_02.png
[3]: ./media/15five-tutorial/tutorial_general_03.png
[4]: ./media/15five-tutorial/tutorial_general_04.png

[100]: ./media/15five-tutorial/tutorial_general_100.png

[200]: ./media/15five-tutorial/tutorial_general_200.png
[201]: ./media/15five-tutorial/tutorial_general_201.png
[202]: ./media/15five-tutorial/tutorial_general_202.png
[203]: ./media/15five-tutorial/tutorial_general_203.png

