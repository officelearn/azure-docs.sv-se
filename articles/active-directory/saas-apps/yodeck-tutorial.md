---
title: 'Självstudier: Azure Active Directory-integrering med Yodeck | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 071054e2ec5fead3ce6181925b3c5f4e99a26736
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164234"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Självstudier: Azure Active Directory-integrering med Yodeck

I den här självstudien får du lära dig hur du integrerar Yodeck med Azure Active Directory (AD Azure).

Integrera Yodeck med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Yodeck.
- Du kan aktivera användarna att automatiskt få loggat in på Yodeck (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Yodeck, behöver du följande objekt:

- En Azure AD-prenumeration
- En Yodeck enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Yodeck från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-yodeck-from-the-gallery"></a>Att lägga till Yodeck från galleriet
För att konfigurera integrering av Yodeck i Azure AD, som du behöver lägga till Yodeck från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Yodeck från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Yodeck**väljer **Yodeck** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Yodeck i resultatlistan](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Yodeck baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Yodeck är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Yodeck upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Yodeck, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Yodeck](#create-a-yodeck-test-user)**  – du har en motsvarighet för Britta Simon i Yodeck som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Yodeck program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Yodeck:**

1. I Azure-portalen på den **Yodeck** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

1. På den **Yodeck domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Yodeck domän och URL: er med enkel inloggning för information](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    I den **identifierare (entitets-ID)** textrutan anger du URL: `https://app.yodeck.com/api/v1/account/metadata/`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Yodeck domän och URL: er med enkel inloggning för information](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://app.yodeck.com/login`

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/yodeck-tutorial/tutorial_general_400.png)
    
1. I ett annat webbläsarfönster logga du in på webbplatsen Yodeck företag som administratör.

1. Klicka på **användarinställningar** alternativet form det övre högra hörnet av sidan och väljer **kontoinställningar**.

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure1.png)

1. Välj **SAML** och utför följande steg:

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure2.png)

    a. Välj **importera från URL: en**.

    b. I den **URL** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat från Azure-portalen och klicka på **Import**.
    
    c. När du har importerat **Appfederationsmetadata**, återstående fält fylla i automatiskt.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/yodeck-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/yodeck-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/yodeck-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/yodeck-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-yodeck-test-user"></a>Skapa en Yodeck testanvändare

Om du vill aktivera Azure AD-användare att logga in på Yodeck, måste de etableras i Yodeck.
När det gäller Yodeck är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Yodeck företag som administratör.

1. Klicka på **användarinställningar** alternativet form det övre högra hörnet av sidan och väljer **användare**.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user1.png)

1. Klicka på **+ användare** att öppna den **användarinformation** fliken.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user2.png)

1. På den **användarinformation** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/yodeck-tutorial/user3.png)

    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet: **Simon**.

    c. I den **e-post** textrutan typ e-postadressen för användaren som **brittasimon@contoso.com**.

    d. Välj lämplig **behörigheterna** alternativet enligt organisationens behov.
    
    e. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yodeck.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Yodeck, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Yodeck**.

    ![Länken Yodeck i listan med program](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Yodeck i åtkomstpanelen du bör få automatiskt loggat in på ditt Yodeck program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

