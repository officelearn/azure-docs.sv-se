---
title: 'Självstudier: Azure Active Directory-integration med PlanMyLeave | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PlanMyLeave.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: jeedes
ms.openlocfilehash: 2f5dde2d99844ecc71d72207296cf8c7dac29e46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424348"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Självstudier: Azure Active Directory-integration med PlanMyLeave

I den här självstudien får du lära dig hur du integrerar PlanMyLeave med Azure Active Directory (AD Azure).

Integrera PlanMyLeave med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PlanMyLeave.
- Du kan aktivera användarna att automatiskt få loggat in på PlanMyLeave (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PlanMyLeave, behöver du följande objekt:

- En Azure AD-prenumeration
- En PlanMyLeave enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till PlanMyLeave från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-planmyleave-from-the-gallery"></a>Att lägga till PlanMyLeave från galleriet
För att konfigurera integrering av PlanMyLeave i Azure AD, som du behöver lägga till PlanMyLeave från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PlanMyLeave från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **PlanMyLeave**väljer **PlanMyLeave** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![PlanMyLeave i resultatlistan](./media/planmyleave-tutorial/tutorial_planmyleave_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PlanMyLeave baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i PlanMyLeave är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i PlanMyLeave upprättas.

I PlanMyLeave, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med PlanMyLeave, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare PlanMyLeave](#create-a-planmyleave-test-user)**  – du har en motsvarighet för Britta Simon i PlanMyLeave som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PlanMyLeave program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PlanMyLeave:**

1. I Azure-portalen på den **PlanMyLeave** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/planmyleave-tutorial/tutorial_planmyleave_samlbase.png)

1. På den **PlanMyLeave domän och URL: er** avsnittet, utför följande steg:

    ![PlanMyLeave domän och URL: er med enkel inloggning för information](./media/planmyleave-tutorial/tutorial_planmyleave_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company-name>.planmyleave.com/Login.aspx`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [PlanMyLeave klienten supportteamet](mailto:support@planmyleave.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/planmyleave-tutorial/tutorial_planmyleave_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/planmyleave-tutorial/tutorial_general_400.png)

1. På den **PlanMyLeave Configuration** klickar du på **konfigurera PlanMyLeave** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![PlanMyLeave konfiguration](./media/planmyleave-tutorial/tutorial_planmyleave_configure.png) 
1. Logga in på din PlanMyLeave-klient som en administratör i ett annat webbläsarfönster.

1. Gå till **systeminställningarna**. På den **säkerhetshantering** klickar du på avsnittet **företagets SAML-inställningar** .

    ![Konfigurera enkel inloggning på App-sida](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

1. På den **SAML-inställningar** klickar du på ikonen för redigeraren.

    ![Konfigurera enkel inloggning på App-sida](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

1. På den **SAML uppdateringsinställningar** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  I den **inloggnings-URL** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    b.  Öppna din hämtade metadata, kopiera **X509Certificate** och klistra in den till den **certifikat** textrutan.

    c. Ange ”**är aktivera**” till ”**Ja**”.

    d. Klicka på **Spara**. 

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/planmyleave-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/planmyleave-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/planmyleave-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/planmyleave-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-planmyleave-test-user"></a>Skapa en PlanMyLeave testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i PlanMyLeave. PlanMyLeave stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt PlanMyLeave om det inte finns ännu.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [PlanMyLeave supportteamet](mailto:support@planmyleave.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PlanMyLeave.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon PlanMyLeave, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **PlanMyLeave**.

    ![Länken PlanMyLeave i listan med program](./media/planmyleave-tutorial/tutorial_planmyleave_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PlanMyLeave i åtkomstpanelen du bör få automatiskt loggat in på ditt PlanMyLeave program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/planmyleave-tutorial/tutorial_general_203.png

