---
title: 'Självstudier: Azure Active Directory-integration med Vibe HCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vibe HCM-system.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4379bef7-adc9-4b6d-9384-c46d9a914bfe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: jeedes
ms.openlocfilehash: 395f99c986e0a51e8dd2c440d258724e862f275c
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019868"
---
# <a name="tutorial-azure-active-directory-integration-with-vibe-hcm"></a>Självstudier: Azure Active Directory-integration med Vibe HCM-system

I den här självstudien får du lära dig hur du integrerar Vibe HCM-system med Azure Active Directory (AD Azure).

Integrera Vibe HCM-system med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Vibe HCM-system.
- Du kan aktivera användarna att automatiskt få loggat in på Vibe HCM-system (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Vibe HCM, behöver du följande objekt:

- En Azure AD-prenumeration
- En Vibe HCM enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Vibe HCM-system från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-vibe-hcm-from-the-gallery"></a>Att lägga till Vibe HCM-system från galleriet
Om du vill konfigurera integreringen av Vibe HCM-system till Azure AD, som du behöver lägga till Vibe HCM-system från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Vibe HCM-system från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Vibe HCM**väljer **Vibe HCM** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Vibe HCM-system i resultatlistan](./media/vibehcm-tutorial/tutorial_vibehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Vibe HCM-system baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Vibe HCM-system är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Vibe HCM upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Vibe HCM-system, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Vibe HCM](#create-a-vibe-hcm-test-user)**  – du har en motsvarighet för Britta Simon i Vibe HCM-system som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Vibe HCM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Vibe HCM:**

1. I Azure-portalen på den **Vibe HCM** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/vibehcm-tutorial/tutorial_vibehcm_samlbase.png)

3. På den **Vibe HCM-domän och URL: er** avsnittet, he-användaren har inte att utföra några steg eftersom appen är redan förintegrerade med Azure:

    ![Vibe HCM-domän och URL: er med enkel inloggning för information](./media/vibehcm-tutorial/tutorial_vibehcm_url.png)

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Vibe HCM-domän och URL: er med enkel inloggning för information](./media/vibehcm-tutorial/tutorial_vibehcm_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyName>.vibehcm.com/portal.jsp`
     
    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [Vibe HCM-supportteamet](mailto:support@vibehcm.com) att hämta värdet.
 
4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/vibehcm-tutorial/tutorial_vibehcm_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/vibehcm-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **Vibe HCM** sida, som du behöver skicka den kopierade **Appfederationsmetadata** till [Vibe HCM-supportteamet](mailto:support@vibehcm.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/vibehcm-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/vibehcm-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/vibehcm-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/vibehcm-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-vibe-hcm-test-user"></a>Skapa en testanvändare Vibe HCM-system

I det här avsnittet skapar du en användare som kallas Britta Simon i Vibe HCM-system. Arbeta med [Vibe HCM-supportteamet](mailto:support@vibehcm.com) att lägga till användare i Vibe HCM-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vibe HCM-system.

![Tilldela rollen][200] 

**Om du vill tilldela Vibe HCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Vibe HCM**.

    ![Vibe HCM-länk i listan med program](./media/vibehcm-tutorial/tutorial_vibehcm_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Vibe HCM i åtkomstpanelen du bör få automatiskt loggat in på ditt Vibe HCM-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vibehcm-tutorial/tutorial_general_01.png
[2]: ./media/vibehcm-tutorial/tutorial_general_02.png
[3]: ./media/vibehcm-tutorial/tutorial_general_03.png
[4]: ./media/vibehcm-tutorial/tutorial_general_04.png

[100]: ./media/vibehcm-tutorial/tutorial_general_100.png

[200]: ./media/vibehcm-tutorial/tutorial_general_200.png
[201]: ./media/vibehcm-tutorial/tutorial_general_201.png
[202]: ./media/vibehcm-tutorial/tutorial_general_202.png
[203]: ./media/vibehcm-tutorial/tutorial_general_203.png

