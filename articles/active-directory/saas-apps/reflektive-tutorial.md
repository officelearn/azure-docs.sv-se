---
title: 'Självstudier: Azure Active Directory-integration med Reflektive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Reflektive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 799a08b9-1ce6-46d1-9064-aa9f36f6604e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: 228bcc2e43337876b211158f70fe1136de494c85
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041236"
---
# <a name="tutorial-azure-active-directory-integration-with-reflektive"></a>Självstudier: Azure Active Directory-integration med Reflektive

I den här självstudien får du lära dig hur du integrerar Reflektive med Azure Active Directory (AD Azure).

Integrera Reflektive med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Reflektive.
- Du kan aktivera användarna att automatiskt få loggat in på Reflektive (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Reflektive, behöver du följande objekt:

- En Azure AD-prenumeration
- En Reflektive enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Reflektive från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-reflektive-from-the-gallery"></a>Att lägga till Reflektive från galleriet
För att konfigurera integrering av Reflektive i Azure AD, som du behöver lägga till Reflektive från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Reflektive från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Reflektive**väljer **Reflektive** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Reflektive i resultatlistan](./media/reflektive-tutorial/tutorial_reflektive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Reflektive baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Reflektive är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Reflektive upprättas.

I Reflektive, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Reflektive, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Reflektive](#create-a-reflektive-test-user)**  – du har en motsvarighet för Britta Simon i Reflektive som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Reflektive program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Reflektive:**

1. I Azure-portalen på den **Reflektive** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/reflektive-tutorial/tutorial_reflektive_samlbase.png)

3. På den **Reflektive domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Reflektive domän och URL: er med enkel inloggning för information](./media/reflektive-tutorial/tutorial_reflektive_url.png)

    I den **identifierare** textrutan, Använd en av de nedanstående URL enligt bekräftelse från reflekterande supporten:
    | |
    |--|
    | `reflektive.com` |
    | `https://www.reflektive.com/saml/metadata` |

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Reflektive domän och URL: er med enkel inloggning för information](./media/reflektive-tutorial/tutorial_reflektive_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL: `https://www.reflektive.com/app`

    > [!NOTE]
    > För SP läge som du behöver för att hämta e-post-id som är registrerade med [Reflektive supportteamet](https://support@reflektive.com). När du anger ditt ID i den **e-post** textrutan och sedan alternativet för enkel inloggning kommer att aktiveras.
         
5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/reflektive-tutorial/tutorial_reflektive_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/reflektive-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **Reflektive** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Reflektive supportteamet](https://support@reflektive.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/reflektive-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/reflektive-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/reflektive-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/reflektive-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-reflektive-test-user"></a>Skapa en Reflektive testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Reflektive. Arbeta med [Reflektive supportteamet](mailto:support@reflektive.com) att lägga till användare i Reflektive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Reflektive.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Reflektive, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Reflektive**.

    ![Länken Reflektive i listan med program](./media/reflektive-tutorial/tutorial_reflektive_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Reflektive i åtkomstpanelen du bör få automatiskt loggat in på ditt Reflektive program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/reflektive-tutorial/tutorial_general_01.png
[2]: ./media/reflektive-tutorial/tutorial_general_02.png
[3]: ./media/reflektive-tutorial/tutorial_general_03.png
[4]: ./media/reflektive-tutorial/tutorial_general_04.png

[100]: ./media/reflektive-tutorial/tutorial_general_100.png

[200]: ./media/reflektive-tutorial/tutorial_general_200.png
[201]: ./media/reflektive-tutorial/tutorial_general_201.png
[202]: ./media/reflektive-tutorial/tutorial_general_202.png
[203]: ./media/reflektive-tutorial/tutorial_general_203.png

