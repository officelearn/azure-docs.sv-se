---
title: 'Självstudier: Azure Active Directory-integrering med sås Labs - Mobile- och Web testning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och sås Labs - Mobile- och Web testning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 55d84256f408e80600308ede22dbaa903b070d90
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266101"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Självstudier: Azure Active Directory-integrering med sås Labs - Mobile- och webb-testning

I den här självstudien får du lära dig hur du integrerar sås Labs - Mobile- och Web testning med Azure Active Directory (AD Azure).

Integrera sås Labs - Mobile- och Web testning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till sås Labs - Mobile- och Web testning.
- Du kan aktivera användarna att automatiskt få loggat in på sås Labs - Mobile- och Web testning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med sås Labs - Mobile- och webb-testning, behöver du följande objekt:

- En Azure AD-prenumeration
- En sås Labs - Mobilappar och webbprogram testa enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till sås Labs - Mobile- och Web testning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Att lägga till sås Labs - Mobile- och Web testning från galleriet
Om du vill konfigurera integreringen av sås Labs - Mobile- och Web testning i Azure AD, som du behöver lägga till sås Labs - Mobile- och Web testning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till sås Labs - Mobile- och Web testning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **sås Labs - Mobile- och Web testning**väljer **sås Labs - Mobile- och Web testning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Sås Labs - Mobile- och Web testning i resultatlistan](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med sås Labs, Mobile och Web testning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i sås Labs - Mobile- och Web testning är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i sås Labs - Mobile- och Web testning upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med sås Labs - Mobile- och webb-testning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en sås Labs - Mobile- och Web testning testanvändare](#create-a-sauce-labs---mobile-and-web-testing-test-user)**  – du har en motsvarighet för Britta Simon i sås Labs - Mobile- och Web testning som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din sås Labs - Mobile och testa program.

**Om du vill konfigurera Azure utför AD enkel inloggning med sås Labs - Mobile- och webb-testning, du följande steg:**

1. I Azure-portalen på den **sås Labs - Mobile- och Web testning** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. På den **sås Labs - Mobile och Web testning domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Sås Labs - Mobile och Web testning domän och URL: er enkel inloggning för information](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster, loggar du in dina sås labb - Mobilappar och webbprogram testa företagets plats som administratör.

7. Klicka på den **Användarikon** och välj **Team Management** fliken.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Ange din **domännamn** i textrutan.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Klicka på **konfigurera** fliken.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. I den **Konfigurera enkel inloggning** avsnittet, utför följande steg.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klicka på **Bläddra** ladda upp den hämtade metadata från Azure AD.

    b. Välj den **TILLÅT etablering av just** kryssrutan.

    c. Clcik **spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Skapa en sås Labs - Mobile- och Web testning testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i sås Labs - Mobile- och Web testning. Sås Labs - Mobile och Web testning stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt sås Labs - Mobile- och Web testa om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [sås Labs - Mobile- och Web testning supportteam](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till sås Labs - Mobile- och Web testning.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon sås Labs - Mobile- och webb-testning, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **sås Labs - Mobile- och Web testning**.

    ![Sås Labs - Mobile- och Web testning länken i listan med program](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på sås Labs - Mobile- och Web testning panelen i åtkomstpanelen, du bör få automatiskt loggat in på dina sås labb - Mobile och testa program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png