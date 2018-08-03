---
title: 'Självstudier: Azure Active Directory-integration med XaitPorter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: 12fb8e5b2b940c48de766a48f59ed0cc342b5356
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421074"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Självstudier: Azure Active Directory-integration med XaitPorter

I den här självstudien får du lära dig hur du integrerar XaitPorter med Azure Active Directory (AD Azure).

Integrera XaitPorter med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till XaitPorter.
- Du kan aktivera användarna att automatiskt få loggat in på XaitPorter (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med XaitPorter, behöver du följande objekt:

- En Azure AD-prenumeration
- En XaitPorter enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till XaitPorter från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-xaitporter-from-the-gallery"></a>Att lägga till XaitPorter från galleriet
För att konfigurera integrering av XaitPorter i Azure AD, som du behöver lägga till XaitPorter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till XaitPorter från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **XaitPorter**väljer **XaitPorter** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![XaitPorter i resultatlistan](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med XaitPorter baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i XaitPorter är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i XaitPorter upprättas.

I XaitPorter, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med XaitPorter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare XaitPorter](#create-a-xaitporter-test-user)**  – du har en motsvarighet för Britta Simon i XaitPorter som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt XaitPorter program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med XaitPorter:**

1. I Azure-portalen på den **XaitPorter** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. På den **XaitPorter domän och URL: er** avsnittet, utför följande steg:

    ![XaitPorter domän och URL: er med enkel inloggning för information](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.xaitporter.com/saml/login`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [XaitPorter klienten supportteamet](https://www.xait.com/support/) att hämta dessa värden.
     
1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar. 

    ![Länk för hämtning av certifikat](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Ange den **IP-adress** eller **Appfederationsmetadata** till den [SmartRecruiters supportteam](https://www.smartrecruiters.com/about-us/contact-us/), så att XaitPorter kan se till att IP-adressen kan nås från din Konfigurera listan över godkända på sin sida XaitPorter-instans. 

1. I ett annat webbläsarfönster logga du in på webbplatsen XaitPorter företag som administratör.

1. Klicka på **Admin**.

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user1.png)

1. Välj **hantera enkel inloggning** från den **systeminställningarna** listrutan.

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user2.png)

1. I den **hantera enkel inloggning** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user3.png)

    a. Välj **aktivera enkel inloggning för autentisering**.

    b. I **identitet providerinställningar** textrutan klistra in **Appfederationsmetadata** som du har kopierat från Azure-portalen och klicka på **hämta**.

    c. Välj **Aktivera skapa automatiskt användare**.

    d. Klicka på **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/xaitporter-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-xaitporter-test-user"></a>Skapa en XaitPorter testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i XaitPorter. Arbeta med [XaitPorter klienten supportteamet](https://www.xait.com/support/) att lägga till användare i XaitPorter-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till XaitPorter.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon XaitPorter, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **XaitPorter**.

    ![Länken XaitPorter i listan med program](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen XaitPorter i åtkomstpanelen du bör få automatiskt loggat in på ditt XaitPorter program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

