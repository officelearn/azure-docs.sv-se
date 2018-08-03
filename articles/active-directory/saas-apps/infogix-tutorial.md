---
title: 'Självstudier: Azure Active Directory-integration med Infogix Data3Sixty styr | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Infogix Data3Sixty styr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3e54ade44828bf1e26c310a14ae401fe8ae33229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437673"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Självstudier: Azure Active Directory-integration med Infogix Data3Sixty styr

I den här självstudien får du lära dig hur du integrerar Infogix Data3Sixty styr med Azure Active Directory (AD Azure).

Integrera Infogix Data3Sixty styr med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Infogix Data3Sixty styr.
- Du kan aktivera användarna att automatiskt få loggat in på Infogix Data3Sixty styr (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Infogix Data3Sixty styr, behöver du följande objekt:

- En Azure AD-prenumeration
- En Infogix Data3Sixty styr enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Infogix Data3Sixty styr från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Att lägga till Infogix Data3Sixty styr från galleriet
För att konfigurera integrering av Infogix Data3Sixty styr i Azure AD, som du behöver lägga till Infogix Data3Sixty styr från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Infogix Data3Sixty styr från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Infogix Data3Sixty styr**väljer **Infogix Data3Sixty styr** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Infogix Data3Sixty styr i resultatlistan](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Infogix Data3Sixty styr baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Infogix Data3Sixty styr är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Infogix Data3Sixty styr upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Infogix Data3Sixty styr, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Infogix Data3Sixty styr](#create-an-infogix-data3sixty-govern-test-user)**  – du har en motsvarighet för Britta Simon i Infogix Data3Sixty styr som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Infogix Data3Sixty styr program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Infogix Data3Sixty styr:**

1. I Azure-portalen på den **Infogix Data3Sixty styr** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

1. På den **Infogix Data3Sixty styr domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Infogix Data3Sixty styr domän och URL: er med enkel inloggning för information](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://data3sixty.com/ui`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.data3sixty.com/sso/acs`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Infogix Data3Sixty styr domän och URL: er med enkel inloggning för information](./media/infogix-tutorial/tutorial_infogix_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL. Kontakta [Infogix Data3Sixty styr klienten supportteamet](mailto:data3sixtysupport@infogix.com) att hämta dessa värden.

1. Infogix Data3Sixty styra program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning attb](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | Förnamn           | User.givenName |
    | Efternamn        | User.surname |
    | användarnamn       | User.Mail    |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/infogix-tutorial/tutorial_infogix_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/infogix-tutorial/tutorial_general_400.png)
    
1. På den **Infogix Data3Sixty styr Configuration** klickar du på **konfigurera Infogix Data3Sixty styr** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Infogix Data3Sixty styr konfiguration](./media/infogix-tutorial/tutorial_infogix_configure.png) 

1. Att konfigurera enkel inloggning på **Infogix Data3Sixty styr** sida, som du behöver skicka de hämtade **certifikat (Raw), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [ Infogix Data3Sixty styr supportteamet](mailto:data3sixtysupport@infogix.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/infogix-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/infogix-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/infogix-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/infogix-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Skapa en Infogix Data3Sixty styr testanvändare


Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Infogix Data3Sixty styr. Infogix Data3Sixty styr stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Infogix Data3Sixty styr om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Infogix Data3Sixty styr supportteamet](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Infogix Data3Sixty styr.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Infogix Data3Sixty styr, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Infogix Data3Sixty styr**.

    ![Länken Infogix Data3Sixty styr i listan med program](./media/infogix-tutorial/tutorial_infogix_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Infogix Data3Sixty styr i åtkomstpanelen du bör få automatiskt loggat in på ditt Infogix Data3Sixty styr program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

