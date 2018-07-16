---
title: 'Självstudier: Azure Active Directory-integration med QPrism | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 9b37c6d1c1c2e7ec002ac1b4ea5768c8972dd9e8
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040003"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Självstudier: Azure Active Directory-integration med QPrism

I den här självstudien får du lära dig hur du integrerar QPrism med Azure Active Directory (AD Azure).

Integrera QPrism med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till QPrism.
- Du kan aktivera användarna att automatiskt få loggat in på QPrism (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med QPrism, behöver du följande objekt:

- En Azure AD-prenumeration
- En QPrism enkel inloggning aktiverat prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till QPrism från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-qprism-from-the-gallery"></a>Lägg till QPrism från galleriet
För att konfigurera integrering av QPrism i Azure AD, som du behöver lägga till QPrism från galleriet i din lista över hanterade SaaS-appar.

**Lägga till QPrism från galleriet:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Om du vill lägga till ett nytt program, överst i dialogrutan, Välj **nytt program**.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **QPrism**, och välj **QPrism** resultatet panelen. Klicka sedan på **Lägg till** att lägga till programmet.

    ![QPrism i resultatlistan](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med QPrism, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i QPrism är att en användare i Azure AD. Med andra ord måste det finnas ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i QPrism.

För att etablera den här relationen i QPrism, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn**.

Om du vill konfigurera och testa Azure AD enkel inloggning med QPrism, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare QPrism](#create-a-qprism-test-user) har en motsvarighet för Britta Simon i QPrism som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt QPrism program.

1. I Azure-portalen på den **QPrism** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

3. I den **QPrism domän och URL: er** avsnittet, gör du följande:

    ![QPrism domän och URL: er med enkel inloggning för information](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. I den **inloggnings-URL** text skriver en URL som använder följande mönster: `https://<customer domain>.qmyzone.com/login`

    b. I den **identifierare** text skriver en URL som använder följande mönster: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med det faktiska ID: t och inloggnings-URL. Kontakta [QPrism klienten supportteamet](mailto:qsupport-ce@quatrro.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

     ![Länk för hämtning av certifikat](./media/qprism-tutorial/tutorial_qprism_certificate.png)

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/qprism-tutorial/tutorial_general_400.png)
    
6. Att konfigurera enkel inloggning på **QPrism** sida, som du behöver skicka den **Appfederationsmetadata** till [QPrism supportteamet](mailto:qsupport-ce@quatrro.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/qprism-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/qprism-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan överst i den **alla användare** dialogrutan **Lägg till**.

    ![Knappen Lägg till](./media/qprism-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan Gör följande:

    ![Dialogrutan användare](./media/qprism-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-qprism-test-user"></a>Skapa en QPrism testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i QPrism. Arbeta med den [QPrism supportteamet](mailto:qsupport-ce@quatrro.com) att lägga till användare i QPrism-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till QPrism.

![Tilldela rollen][200] 

**Att tilldela QPrism Britta Simon:**

1. Öppna vyn program i Azure-portalen och gå sedan till vyn directory. Gå till **företagsprogram**, och välj **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **QPrism**.

    ![Länken QPrism i listan med program](./media/qprism-tutorial/tutorial_qprism_app.png)  

3. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till**. Sedan, under **Lägg till tilldelning**väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. På den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. På den **användare och grupper** dialogrutan **Välj**.

7. Under **Lägg till tilldelning**väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

I åtkomstpanelen, när du väljer panelen QPrism bör du få automatiskt loggat in på ditt QPrism program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

