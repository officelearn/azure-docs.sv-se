---
title: 'Självstudier: Azure Active Directory-integrering med Coupa | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: 50d2e6a7ae1f34232a20e244ac65c2d3678f4f97
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153938"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudier: Azure Active Directory-integrering med Coupa

I den här självstudien får du lära dig hur du integrerar Coupa med Azure Active Directory (AD Azure).

Integrera Coupa med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Coupa.
- Du kan aktivera användarna att automatiskt få loggat in på Coupa (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Coupa, behöver du följande objekt:

- En Azure AD-prenumeration
- En Coupa enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Coupa från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-coupa-from-the-gallery"></a>Att lägga till Coupa från galleriet
För att konfigurera integrering av Coupa i Azure AD, som du behöver lägga till Coupa från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Coupa från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Coupa**väljer **Coupa** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Coupa i resultatlistan](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Coupa baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Coupa är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Coupa upprättas.

I Coupa, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Coupa, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Coupa](#create-a-coupa-test-user)**  – du har en motsvarighet för Britta Simon i Coupa som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Coupa program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Coupa:**

1. I Azure-portalen på den **Coupa** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

1. På den **Coupa domän och URL: er** avsnittet, utför följande steg:

    ![Coupa domän och URL: er med enkel inloggning för information](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Coupa klienten supportteamet](https://success.coupa.com/Support/Contact_Us?) att hämta det här värdet.

    b. I textrutan **Identifierare** anger du URL:en:

    | Miljö  | URL |
    |:-------------|----|
    | Begränsat läge | `devsso35.coupahost.com`|
    | Produktion | `prdsso40.coupahost.com`|
    | | |

    c. I textrutan **Svars-URL** anger du URL:en:

    | Miljö | URL |
    |------------- |----|
    | Begränsat läge | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | Produktion | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/coupa-tutorial/tutorial_general_400.png)

1. Logga in på webbplatsen Coupa företag som administratör.

1. Gå till **installationsprogrammet \> säkerhetskontroll**.

   ![Säkerhetskontroller](./media/coupa-tutorial/ic791900.png "säkerhetskontroller")

1. I den **logga in med autentiseringsuppgifter för Coupa** avsnittet, utför följande steg:

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

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/coupa-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/coupa-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/coupa-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-coupa-test-user"></a>Skapa en Coupa testanvändare

För att aktivera Azure AD-användare att logga in på Coupa, måste de etableras i Coupa.  

* När det gäller Coupa är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Coupa** företagets plats som administratör.

1. Klicka på menyn längst upp **installationsprogrammet**, och klicka sedan på **användare**.

   ![Användare](./media/coupa-tutorial/ic791908.png "Användare")

1. Klicka på **Skapa**.

   ![Skapa användare](./media/coupa-tutorial/ic791909.png "skapa användare")

1. I den **användaren skapa** avsnittet, utför följande steg:

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

1. I listan med program väljer **Coupa**.

    ![Länken Coupa i listan med program](./media/coupa-tutorial/tutorial_coupa_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

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
