---
title: 'Självstudier: Azure Active Directory-integration med Dovetale | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dovetale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81da50c3-df94-458a-8b6a-a30827ee6358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: dd2aa699c22518ebbe7f29ba8dfd296da7385476
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "40162134"
---
# <a name="tutorial-azure-active-directory-integration-with-dovetale"></a>Självstudier: Azure Active Directory-integration med Dovetale

I den här självstudien får du lära dig hur du integrerar Dovetale med Azure Active Directory (AD Azure).

Integrera Dovetale med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dovetale.
- Du kan aktivera användarna att automatiskt få loggat in på Dovetale (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Dovetale, behöver du följande objekt:

- En Azure AD-prenumeration
- En Dovetale enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Dovetale från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dovetale-from-the-gallery"></a>Att lägga till Dovetale från galleriet

För att konfigurera integrering av Dovetale i Azure AD, som du behöver lägga till Dovetale från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Dovetale från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dovetale**väljer **Dovetale** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Dovetale i resultatlistan](./media/dovetale-tutorial/tutorial_dovetale_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Dovetale baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Dovetale är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Dovetale upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dovetale, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Dovetale](#create-a-dovetale-test-user)**  – du har en motsvarighet för Britta Simon i Dovetale som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Dovetale program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dovetale:**

1. I Azure-portalen på den **Dovetale** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/dovetale-tutorial/tutorial_dovetale_samlbase.png)

3. På den **Dovetale domän och URL: er** om du vill konfigurera programmet i **IDP** initierad läge, användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure:

    ![Dovetale domän och URL: er med enkel inloggning för information](./media/dovetale-tutorial/tutorial_dovetale_url1.png)

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Dovetale domän och URL: er med enkel inloggning för information](./media/dovetale-tutorial/tutorial_dovetale_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `<COMPANYNAME>.dovetale.com`

    > [!NOTE]
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Dovetale klienten supportteamet](mailto:support@dovetale.com) att hämta det här värdet.

5. Dovetale program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/dovetale-tutorial/tutorial_attribute.png)

6. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde |
    | ---------------| --------------- |
    | e-post | User.Mail |
    | Förnamn | User.givenName |
    | namn | User.userPrincipalName |
    | Efternamn | User.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/dovetale-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/dovetale-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver den **attributnamnet** visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **namnområde** värdet tomt.

    e. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/dovetale-tutorial/tutorial_dovetale_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/dovetale-tutorial/tutorial_general_400.png)

9. På den **Dovetale Configuration** klickar du på **konfigurera Dovetale** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Dovetale konfiguration](./media/dovetale-tutorial/tutorial_dovetale_configure.png)

10. Att konfigurera enkel inloggning på **Dovetale** sida, som du behöver skicka den **Appfederationsmetadata, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Dovetale supportteamet](mailto:support@dovetale.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dovetale-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dovetale-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dovetale-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dovetale-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-dovetale-test-user"></a>Skapa en Dovetale testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Dovetale. Dovetale stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Dovetale om det inte finns ännu.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [Dovetale supportteamet](mailto:support@dovetale.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dovetale.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Dovetale, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Dovetale**.

    ![Länken Dovetale i listan med program](./media/dovetale-tutorial/tutorial_dovetale_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Dovetale i åtkomstpanelen du bör få automatiskt loggat in på ditt Dovetale program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dovetale-tutorial/tutorial_general_01.png
[2]: ./media/dovetale-tutorial/tutorial_general_02.png
[3]: ./media/dovetale-tutorial/tutorial_general_03.png
[4]: ./media/dovetale-tutorial/tutorial_general_04.png

[100]: ./media/dovetale-tutorial/tutorial_general_100.png

[200]: ./media/dovetale-tutorial/tutorial_general_200.png
[201]: ./media/dovetale-tutorial/tutorial_general_201.png
[202]: ./media/dovetale-tutorial/tutorial_general_202.png
[203]: ./media/dovetale-tutorial/tutorial_general_203.png