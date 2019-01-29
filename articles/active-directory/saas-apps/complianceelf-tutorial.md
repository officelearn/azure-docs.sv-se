---
title: 'Självstudier: Azure Active Directory-integrering med efterlevnad Tomtenisse | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och efterlevnad Tomtenisse.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 509bec49840537dbb5bb7f0ec69cc4dfb750244a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189433"
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Självstudier: Azure Active Directory-integrering med efterlevnad Tomtenisse

Lär dig hur du integrerar efterlevnad Tomtenisse med Azure Active Directory (AD Azure) i den här självstudien.

Integrera efterlevnad Tomtenisse med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till efterlevnad Tomtenisse.
- Du kan aktivera användarna att automatiskt få loggat in på efterlevnad Tomtenisse (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med efterlevnad Tomtenisse, behöver du följande objekt:

- En Azure AD-prenumeration
- En efterlevnad Tomtenisse enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till efterlevnad Tomtenisse från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-compliance-elf-from-the-gallery"></a>Att lägga till efterlevnad Tomtenisse från galleriet
För att konfigurera integrering av efterlevnad Tomtenisse i Azure AD, som du behöver lägga till efterlevnad Tomtenisse från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till efterlevnad Tomtenisse från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **efterlevnad Tomtenisse**väljer **efterlevnad Tomtenisse** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Efterlevnad Tomtenisse i listan med resultat](./media/complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med efterlevnad Tomtenisse baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i efterlevnad Tomtenisse är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i efterlevnad Tomtenisse upprättas.

I efterlevnad Tomtenisse tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med efterlevnad Tomtenisse, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare efterlevnad Tomtenisse](#create-a-compliance-elf-test-user)**  – du har en motsvarighet för Britta Simon i efterlevnad Tomtenisse som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för efterlevnad Tomtenisse.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med efterlevnad Tomtenisse:**

1. I Azure-portalen på den **efterlevnad Tomtenisse** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. På den **efterlevnad Tomtenisse domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Efterlevnad Tomtenisse domän och URL: er med enkel inloggning för information](./media/complianceelf-tutorial/tutorial_complianceelf_url.png)

    I den **identifierare** textrutan anger du ett URL: en som: `https://sso.cordium.com`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Efterlevnad Tomtenisse domän och URL: er som enkel inloggning](./media/complianceelf-tutorial/tutorial_complianceelf_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.complianceelf.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera den här värden med den faktiska inloggnings-URL. Kontakta [efterlevnad Tomtenisse supportteamet](mailto:support@complianceelf.com) att hämta det här värdet.

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/complianceelf-tutorial/tutorial_metadataurl.png)
     
6. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/complianceelf-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **efterlevnad Tomtenisse** sida, som du behöver skicka den **Appfederationsmetadata** till [efterlevnad Tomtenisse supportteamet](mailto:support@complianceelf.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/complianceelf-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/complianceelf-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/complianceelf-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/complianceelf-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-compliance-elf-test-user"></a>Skapa en testanvändare efterlevnad Tomtenisse

I det här avsnittet skapar du en användare som kallas Britta Simon i efterlevnad Tomtenisse. Arbeta med [efterlevnad Tomtenisse supportteamet](mailto:support@complianceelf.com) att lägga till användare i efterlevnad Tomtenisse-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till efterlevnad Tomtenisse.

![Tilldela rollen][200] 

**Om du vill tilldela efterlevnad Tomtenisse Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **efterlevnad Tomtenisse**.

    ![Länken efterlevnad Tomtenisse i listan med program](./media/complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen efterlevnad Tomtenisse i åtkomstpanelen du bör få automatiskt loggat in på ditt efterlevnad Tomtenisse program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/complianceelf-tutorial/tutorial_general_203.png

