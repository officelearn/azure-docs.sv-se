---
title: 'Självstudier: Azure Active Directory-integrering med Innovation Hub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Innovation Hub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d72e4da0-0123-409b-96c2-e613f3f83fb1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 046673abd2b17118732439f2895e36ca18ad8321
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052853"
---
# <a name="tutorial-azure-active-directory-integration-with-innovation-hub"></a>Självstudier: Azure Active Directory-integrering med Innovation Hub

I den här självstudien får du lära dig hur du integrerar Innovation Hub med Azure Active Directory (AD Azure).

Integrera Innovation Hub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Innovation Hub.
- Du kan aktivera användarna att automatiskt få loggat in på Innovation Hub (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Innovation Hub, behöver du följande objekt:

- En Azure AD-prenumeration
- En Innovation Hub enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Innovation Hub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-innovation-hub-from-the-gallery"></a>Att lägga till Innovation Hub från galleriet
För att konfigurera integrering av Innovation Hub i Azure AD, som du behöver lägga till Innovation Hub från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Innovation Hub från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Innovation Hub**väljer **Innovation Hub** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Innovation hubben i listan med resultat](./media/innovationhub-tutorial/tutorial_innovationhub_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Innovation hubb baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Innovation Hub är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Innovation Hub upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Innovation Hub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Innovation Hub](#create-an-innovation-hub-test-user)**  – du har en motsvarighet för Britta Simon i Innovation Hub som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Innovation Hub.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Innovation Hub:**

1. I Azure-portalen på den **Innovation Hub** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/innovationhub-tutorial/tutorial_innovationhub_samlbase.png)

3. På den **Innovation Hub domän och URL: er** avsnittet, utför följande steg:

    ![Innovation Hub domän och URL: er med enkel inloggning för information](./media/innovationhub-tutorial/tutorial_innovationhub_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<domainname>.innohb.com/auth/saml2/login`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<domainname>.innohb.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Innovation Hub Client supportteamet](mailto:support@readify.net) att hämta dessa värden.

4. Innovation Hub program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/attribute.png)

5. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde | Namespace värde|
    | ---------------| --------------- |----------------|
    | DisplayName | User.userPrincipalName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Från den **Namespace värdet** anger namnområdesvärdet som visas för den raden.

    e. Klicka på **OK**.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/innovationhub-tutorial/tutorial_innovationhub_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/innovationhub-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **Innovation Hub** sida, som du behöver skicka den kopierade **Url för Federation Metadata** till [Innovation Hub supportteamet](mailto:support@readify.net). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/innovationhub-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/innovationhub-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/innovationhub-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/innovationhub-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-innovation-hub-test-user"></a>Skapa en testanvändare Innovation Hub

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Innovation Hub. Innovation Hub stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Innovation Hub om den inte finns.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Innovation Hub supportteamet](mailto:support@readify.net).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Innovation Hub.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Innovation Hub, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Innovation Hub**.

    ![Innovation Hub-länk i listan med program](./media/innovationhub-tutorial/tutorial_innovationhub_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Innovation Hub i åtkomstpanelen du bör få automatiskt loggat in på ditt Innovation Hub-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
<!--Image references-->

[1]: ./media/innovationhub-tutorial/tutorial_general_01.png
[2]: ./media/innovationhub-tutorial/tutorial_general_02.png
[3]: ./media/innovationhub-tutorial/tutorial_general_03.png
[4]: ./media/innovationhub-tutorial/tutorial_general_04.png

[100]: ./media/innovationhub-tutorial/tutorial_general_100.png

[200]: ./media/innovationhub-tutorial/tutorial_general_200.png
[201]: ./media/innovationhub-tutorial/tutorial_general_201.png
[202]: ./media/innovationhub-tutorial/tutorial_general_202.png
[203]: ./media/innovationhub-tutorial/tutorial_general_203.png

