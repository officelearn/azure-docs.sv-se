---
title: 'Självstudier: Azure Active Directory-integration med RealtimeBoard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RealtimeBoard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a37fc1c0-4bae-4173-989b-00de53a0076f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: 00b0844deb8cc81f770f6c98f8b020f1402d2ff7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045360"
---
# <a name="tutorial-azure-active-directory-integration-with-realtimeboard"></a>Självstudier: Azure Active Directory-integration med RealtimeBoard

I den här självstudien får du lära dig hur du integrerar RealtimeBoard med Azure Active Directory (AD Azure).

Integrera RealtimeBoard med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till RealtimeBoard.
- Du kan aktivera användarna att automatiskt få loggat in på RealtimeBoard (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RealtimeBoard, behöver du följande objekt:

- En Azure AD-prenumeration
- En RealtimeBoard enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till RealtimeBoard från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-realtimeboard-from-the-gallery"></a>Att lägga till RealtimeBoard från galleriet
För att konfigurera integrering av RealtimeBoard i Azure AD, som du behöver lägga till RealtimeBoard från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till RealtimeBoard från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **RealtimeBoard**väljer **RealtimeBoard** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![RealtimeBoard i resultatlistan](./media/realtimeboard-tutorial/tutorial_realtimeboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med RealtimeBoard baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i RealtimeBoard är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i RealtimeBoard upprättas.

I RealtimeBoard, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med RealtimeBoard, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare RealtimeBoard](#create-a-realtimeboard-test-user)**  – du har en motsvarighet för Britta Simon i RealtimeBoard som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt RealtimeBoard program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med RealtimeBoard:**

1. I Azure-portalen på den **RealtimeBoard** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/realtimeboard-tutorial/tutorial_realtimeboard_samlbase.png)

3. På den **RealtimeBoard domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![RealtimeBoard domän och URL: er med enkel inloggning för information](./media/realtimeboard-tutorial/tutorial_realtimeboard_url.png)

    I den **identifierare** textrutan anger du ett URL: en som: `https://realtimeboard.com/`

4. Kontrollera **visa avancerade URL-inställningar**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/realtimeboard-tutorial/tutorial_realtimeboard_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://realtimeboard.com/sso/saml`

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/realtimeboard-tutorial/tutorial_realtimeboard_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/realtimeboard-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på den **RealtimeBoard** sida, följer du de [RealtimeBoard instruktioner](https://help.realtimeboard.com/support/solutions/articles/11000023465-saml-based-single-sign-on-), och använda data från din hämtade **XML-Metadata för**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/realtimeboard-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/realtimeboard-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/realtimeboard-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/realtimeboard-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-realtimeboard-test-user"></a>Skapa en RealtimeBoard testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i RealtimeBoard. RealtimeBoard stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. Om en användare inte redan finns i RealtimeBoard, skapas en ny när du försöker komma åt RealtimeBoard.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RealtimeBoard.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon RealtimeBoard, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **RealtimeBoard**.

    ![Länken RealtimeBoard i listan med program](./media/realtimeboard-tutorial/tutorial_realtimeboard_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen RealtimeBoard i åtkomstpanelen du bör få automatiskt loggat in på ditt RealtimeBoard program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/realtimeboard-tutorial/tutorial_general_01.png
[2]: ./media/realtimeboard-tutorial/tutorial_general_02.png
[3]: ./media/realtimeboard-tutorial/tutorial_general_03.png
[4]: ./media/realtimeboard-tutorial/tutorial_general_04.png

[100]: ./media/realtimeboard-tutorial/tutorial_general_100.png

[200]: ./media/realtimeboard-tutorial/tutorial_general_200.png
[201]: ./media/realtimeboard-tutorial/tutorial_general_201.png
[202]: ./media/realtimeboard-tutorial/tutorial_general_202.png
[203]: ./media/realtimeboard-tutorial/tutorial_general_203.png

