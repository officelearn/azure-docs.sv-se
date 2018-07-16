---
title: 'Självstudier: Azure Active Directory-integration med Coupa | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: adad60611f1447b78173368ed137205f077cb8b7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047818"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudier: Azure Active Directory-integration med Coupa

I den här självstudien får du lära dig hur du integrerar Coupa med Azure Active Directory (AD Azure).

Integrera Coupa med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Coupa.
- Du kan aktivera användarna att automatiskt få loggat in på Coupa (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Coupa, behöver du följande objekt:

- En Azure AD-prenumeration
- En Coupa enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Coupa från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-coupa-from-the-gallery"></a>Att lägga till Coupa från galleriet
För att konfigurera integrering av Coupa i Azure AD, som du behöver lägga till Coupa från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Coupa från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Coupa**väljer **Coupa** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Coupa i resultatlistan](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Coupa baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Coupa är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Coupa upprättas.

I Coupa, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Coupa, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Coupa](#create-a-coupa-test-user)**  – du har en motsvarighet för Britta Simon i Coupa som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Coupa program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Coupa:**

1. I Azure-portalen på den **Coupa** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

3. På den **Coupa domän och URL: er** avsnittet, utför följande steg:

    ![Coupa domän och URL: er med enkel inloggning för information](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Coupa klienten supportteamet](https://success.coupa.com/Support/Contact_Us?) att hämta det här värdet.

    b. I den **identifierare** textrutan anger du URL:

    | Miljö  | Webbadress |
    |:-------------|----|
    | Begränsat läge | `devsso35.coupahost.com`|
    | Produktion | `prdsso40.coupahost.com`|
    | | |

    c. I den **svars-URL** textrutan anger du URL:

    | Miljö | Webbadress |
    |------------- |----|
    | Begränsat läge | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | Produktion | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/coupa-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen Coupa företag som administratör.

7. Gå till **installationsprogrammet \> säkerhetskontroll**.

   ![Säkerhetskontroller](./media/coupa-tutorial/ic791900.png "säkerhetskontroller")

8. I den **logga in med autentiseringsuppgifter för Coupa** avsnittet, utför följande steg:

    ![Coupa SP metadata](./media/coupa-tutorial/ic791901.png "Coupa SP metadata")

    a. Välj **logga in med hjälp av SAML**.

    b. Klicka på **Bläddra** att ladda upp de metadata som hämtats från Azure-portalen.

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/coupa-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/coupa-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/coupa-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/coupa-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-coupa-test-user"></a>Skapa en Coupa testanvändare

För att aktivera Azure AD-användare att logga in på Coupa, måste de etableras i Coupa.  

* När det gäller Coupa är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Coupa** företagets plats som administratör.

2. Klicka på menyn längst upp **installationsprogrammet**, och klicka sedan på **användare**.

   ![Användare](./media/coupa-tutorial/ic791908.png "användare")

3. Klicka på **Skapa**.

   ![Skapa användare](./media/coupa-tutorial/ic791909.png "skapa användare")

4. I den **användaren skapa** avsnittet, utför följande steg:

   ![Information om användare](./media/coupa-tutorial/ic791910.png "användarinformation")

   a. Skriv den **inloggning**, **Förnamn**, **efternamn**, **enkel inloggnings-ID**, **e-post** attribut för en giltigt Azure Active Directory-konto som du vill etablera till relaterade textrutor.

   b. Klicka på **Skapa**.

   >[!NOTE]
   >Azure Active Directory-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.
   >

>[!NOTE]
>Du kan använda alla andra Coupa användare konto verktyg för att skapa eller API: er som tillhandahålls av Coupa att etablera AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Coupa.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Coupa, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Coupa**.

    ![Länken Coupa i listan med program](./media/coupa-tutorial/tutorial_coupa_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Coupa i åtkomstpanelen du bör få automatiskt loggat in på ditt Coupa program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png
