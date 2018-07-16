---
title: 'Självstudier: Azure Active Directory-integration med växthusgaser | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och växthusgaser.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b8c224c6c2178e82b7130715df1bfa8b3a0096a5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042919"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Självstudier: Azure Active Directory-integration med växthusgaser

I den här självstudien får du lära dig hur du integrerar växthusgaser med Azure Active Directory (AD Azure).

Integrera växthusgaser med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till växthusgaser.
- Du kan aktivera användarna att automatiskt få loggat in på växthusgaser (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med växthusgaser, behöver du följande objekt:

- En Azure AD-prenumeration
- En växthusgaser enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till växthusgaser från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-greenhouse-from-the-gallery"></a>Att lägga till växthusgaser från galleriet
Om du vill konfigurera integration världens till Azure AD, som du behöver lägga till växthusgaser från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till växthusgaser från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **växthusgaser**väljer **växthusgaser** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Växthusgaser i resultatlistan](./media/greenhouse-tutorial/tutorial_greenhouse_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med växthusgaser baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i växthusgaser är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i växthusgaser upprättas.

I växthus, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med växthusgaser, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare växthusgaser](#create-a-greenhouse-test-user)**  – du har en motsvarighet för Britta Simon i växthusgaser som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt växthusgaser-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med växthusgaser:**

1. I Azure-portalen på den **växthusgaser** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/greenhouse-tutorial/tutorial_greenhouse_samlbase.png)

3. På den **växthusgaser domän och URL: er** avsnittet, utför följande steg:

    ![Växthusgaser domän och URL: er med enkel inloggning för information](./media/greenhouse-tutorial/tutorial_greenhouse_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.greenhouse.io`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.greenhouse.io`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [växthusgaser klienten supportteamet](https://www.greenhouse.io/contact) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/greenhouse-tutorial/tutorial_greenhouse_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/greenhouse-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **växthusgaser** sida, som du behöver skicka de hämtade **XML-Metadata för** till [växthusgaser supportteamet](http://www.greenhouse.io/contact).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/greenhouse-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/greenhouse-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/greenhouse-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/greenhouse-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-greenhouse-test-user"></a>Skapa en testanvändare växthusgaser

För att aktivera Azure AD-användare att logga in på växthusgaser, måste de etableras i växthusgaser. När det gäller växthusgaser är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra växthusgaser användare konto verktyg för att skapa eller API: er som tillhandahålls av växthusgaser att etablera AAD-användarkonton. 

**Utför följande steg för att tillhandahålla en användarkonton:**

1. Logga in på din **växthusgaser** företagets plats som administratör.

2. Klicka på menyn längst upp **konfigurera**, och klicka sedan på **användare**.
   
   ![Användare](./media/greenhouse-tutorial/ic790791.png "användare")

3. Klicka på **nya användare**.
   
   ![Ny användare](./media/greenhouse-tutorial/ic790792.png "ny användare")

4. I den **Lägg till ny användare** avsnittet, utför följande steg:
   
   ![Lägg till ny användare](./media/greenhouse-tutorial/ic790793.png "Lägg till ny användare")

   a. I den **ange användarnas e-postadresser** textrutan skriver du ett giltigt Azure Active Directory-konto som du vill etablera e-postadress.

   b. Klicka på **Spara**.    
   
      >[!NOTE]
      >Azure Active Directory-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till växthusgaser.

![Tilldela rollen][200] 

**Om du vill tilldela växthusgaser Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **växthusgaser**.

    ![Länken växthusgaser i listan med program](./media/greenhouse-tutorial/tutorial_greenhouse_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen växthusgaser i åtkomstpanelen du bör få automatiskt loggat in på ditt växthusgaser-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/greenhouse-tutorial/tutorial_general_01.png
[2]: ./media/greenhouse-tutorial/tutorial_general_02.png
[3]: ./media/greenhouse-tutorial/tutorial_general_03.png
[4]: ./media/greenhouse-tutorial/tutorial_general_04.png

[100]: ./media/greenhouse-tutorial/tutorial_general_100.png

[200]: ./media/greenhouse-tutorial/tutorial_general_200.png
[201]: ./media/greenhouse-tutorial/tutorial_general_201.png
[202]: ./media/greenhouse-tutorial/tutorial_general_202.png
[203]: ./media/greenhouse-tutorial/tutorial_general_203.png

