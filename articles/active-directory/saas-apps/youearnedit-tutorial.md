---
title: 'Självstudier: Azure Active Directory-integration med YouEarnedIt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och YouEarnedIt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: jeedes
ms.openlocfilehash: f860036f1a69b2d1ab6ac8de763a49380f8fe4bf
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055172"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Självstudier: Azure Active Directory-integration med YouEarnedIt

I den här självstudien får du lära dig hur du integrerar YouEarnedIt med Azure Active Directory (AD Azure).

Integrera YouEarnedIt med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till YouEarnedIt.
- Du kan aktivera användarna att automatiskt få loggat in på YouEarnedIt (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med YouEarnedIt, behöver du följande objekt:

- En Azure AD-prenumeration
- En YouEarnedIt enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till YouEarnedIt från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-youearnedit-from-the-gallery"></a>Att lägga till YouEarnedIt från galleriet

För att konfigurera integrering av YouEarnedIt i Azure AD, som du behöver lägga till YouEarnedIt från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till YouEarnedIt från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **YouEarnedt**väljer **YouEarnedt** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![YouEarnedIt i resultatlistan](./media/youearnedit-tutorial/tutorial_youearnedit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med YouEarnedIt baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i YouEarnedIt är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i YouEarnedIt upprättas.

I YouEarnedIt, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med YouEarnedIt, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare YouEarnedIt](#create-a-youearnedit-test-user)**  – du har en motsvarighet för Britta Simon i YouEarnedIt som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt YouEarnedIt program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med YouEarnedIt:**

1. I Azure-portalen på den **YouEarnedIt** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/youearnedit-tutorial/tutorial_youearnedit_samlbase.png)

3. På den **YouEarnedIt domän och URL: er** avsnittet, utför följande steg:

    ![YouEarnedIt domän och URL: er med enkel inloggning för information](./media/youearnedit-tutorial/tutorial_youearnedit_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: 
    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `https://<company name>.youearnedit.com/users/sign_in` |
    | Begränsat läge  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `https://<company name>.youearnedit.com` |
    | Begränsat läge  |`https://<company name>.sandbox.youearnedit.com` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta din tilldelade YouEarnedIt kunden ska lyckas chef för att få dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/youearnedit-tutorial/tutorial_youearnedit_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/youearnedit-tutorial/tutorial_general_400.png)

6. På den **YouEarnedIt Configuration** klickar du på **konfigurera YouEarnedIt** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![YouEarnedIt konfiguration](./media/youearnedit-tutorial/tutorial_youearnedit_configure.png) 

7. Att konfigurera enkel inloggning på **YouEarnedIt** sida, som du behöver skicka de hämtade **Certificate(Base64)** och **SAML enkel inloggning för tjänst-URL** till dina tilldelade YouEarnedIt kunden ska lyckas manager. De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/youearnedit-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/youearnedit-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/youearnedit-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/youearnedit-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-youearnedit-test-user"></a>Skapa en YouEarnedIt testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i YouEarnedIt. Kontakta din tilldelade YouEarnedIt kunden ska lyckas chef att lägga till användare i YouEarnedIt-plattformen.

>[!NOTE]
>YouEarnedIt förväntar sig identitetsprovider att ange en e-postadress eller användarnamn i NameID-attribut. Om en motsvarande användarnamn eller e-postadress finns inte i databasen eller matchar inte exakt misslyckas autentiseringen. Detta kräver att konton importeras till YouEarnedIt-systemet innan SSO-integration (vanligtvis antingen via API: et eller CSV-import).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till YouEarnedIt.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon YouEarnedIt, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **YouEarnedIt**.

    ![Länken YouEarnedIt i listan med program](./media/youearnedit-tutorial/tutorial_youearnedit_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen YouEarnedIt i åtkomstpanelen du bör få automatiskt loggat in på ditt YouEarnedIt program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/youearnedit-tutorial/tutorial_general_04.png

[100]: ./media/youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/youearnedit-tutorial/tutorial_general_201.png
[202]: ./media/youearnedit-tutorial/tutorial_general_202.png
[203]: ./media/youearnedit-tutorial/tutorial_general_203.png