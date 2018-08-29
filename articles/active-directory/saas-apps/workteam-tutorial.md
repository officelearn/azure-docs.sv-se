---
title: 'Självstudier: Azure Active Directory-integration med Workteam | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 8d6ca6395e4f5e1aca361c56e21afc4e6bd1fc0c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132837"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Självstudier: Azure Active Directory-integration med Workteam

I den här självstudien får du lära dig hur du integrerar Workteam med Azure Active Directory (AD Azure).

Integrera Workteam med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workteam.
- Du kan aktivera användarna att automatiskt få loggat in på Workteam (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Workteam, behöver du följande objekt:

- En Azure AD-prenumeration
- En Workteam enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Workteam från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workteam-from-the-gallery"></a>Att lägga till Workteam från galleriet
För att konfigurera integrering av Workteam i Azure AD, som du behöver lägga till Workteam från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workteam från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Workteam**väljer **Workteam** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Workteam i resultatlistan](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workteam baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Workteam är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Workteam upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workteam, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Workteam](#create-a-workteam-test-user)**  – du har en motsvarighet för Britta Simon i Workteam som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Workteam program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workteam:**

1. I Azure-portalen på den **Workteam** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. På den **Workteam domän och URL: er** avsnittet användaren behöver inte utföra några steg eftersom programmet är redan förintegrerade med Azure.

    ![Workteam domän och URL: er med enkel inloggning för information](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Workteam domän och URL: er med enkel inloggning för information](./media/workteam-tutorial/tutorial_workteam_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL: `https://app.workte.am`
     
5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/workteam-tutorial/tutorial_general_400.png)
    
7. På den **Workteam Configuration** klickar du på **konfigurera Workteam** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Workteam konfiguration](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. I ett annat webbläsarfönster, logga in på Workteam som en administratör.

9. I övre högra hörnet klickar du på **profilera logotyp** och klicka sedan på **organisationsinställningar**. 

    ![Workteam inställningar](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. Under **AUTENTISERING** klickar du på **inställningar logotyp**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. På den **SAML-inställningar** utför följande steg:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Välj **SAML IdP** som **AD Azure**.

    b. I den **SAML enkel inloggning för tjänst-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I den **SAML entitets-ID** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    d. I anteckningar, öppna den **Base64-kodade certifikat** som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **SAML-signeringscertifikat (Base64)** box.

    e. Klicka på **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/workteam-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/workteam-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/workteam-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/workteam-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workteam-test-user"></a>Skapa en Workteam testanvändare

Om du vill aktivera Azure AD-användare att logga in på Workteam, måste de etableras i Workteam. I Workteam är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Workteam som en administratör.

2. På överst i mitten på den **organisationsinställningar** klickar du på **användare** och klicka sedan på **ny användare**.

    ![Workteam användare](./media/workteam-tutorial/tutorial_workteam_user.png)

3. På den **nyanställd** utför följande steg:

    ![Workteam ny användare](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. I den **namn** text, ange först namnet på användaren som **Brittasimon**.

    b. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    c. Klicka på **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workteam.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Workteam, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Workteam**.

    ![Länken Workteam i listan med program](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Workteam i åtkomstpanelen du bör få automatiskt loggat in på ditt Workteam program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

