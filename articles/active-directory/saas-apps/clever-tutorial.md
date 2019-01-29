---
title: 'Självstudier: Azure Active Directory-integrering med Clever | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e65f0cb3ef30fb5b001acdb72481c1c3b55ca058
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197321"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Självstudier: Azure Active Directory-integrering med Clever

I den här självstudien får du lära dig hur du integrerar Clever med Azure Active Directory (AD Azure).

Integrera Clever med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Clever.
- Du kan aktivera användarna att automatiskt få loggat in på Clever (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Clever, behöver du följande objekt:

- En Azure AD-prenumeration
- En smarta enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Clever från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-clever-from-the-gallery"></a>Att lägga till Clever från galleriet
För att konfigurera integrering av Clever i Azure AD, som du behöver lägga till Clever från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Clever från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Clever**väljer **Clever** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Smarta i resultatlistan](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Clever baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Clever är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Clever upprättas.

I Clever, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Clever, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en smarta testanvändare](#create-a-clever-test-user)**  – du har en motsvarighet för Britta Simon i Clever som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt smarta program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Clever:**

1. I Azure-portalen på den **Clever** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. På den **smarta domän och URL: er** avsnittet, utför följande steg:

    ![Smarta domän och URL: er med enkel inloggning för information](./media/clever-tutorial/tutorial_clever_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://clever.com/in/<companyname>`

    b. I den **identifierare** textrutan anger du URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_metadataurl.png)

1. Smarta programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar till din **SAML-Tokenattribut** konfiguration.

    Följande skärmbild visar ett exempel på detta.

    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_clever_07.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | Förnamn  | user.givenname |
    | Efternamn  | user.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** textrutan som är tom.
    
    d. Klicka på **OK**.
    
1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/clever-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster logga du in på webbplatsen smarta företagets som administratör.

1. I verktygsfältet klickar du på **direkt inloggning**.

    ![Direkt inloggning](./media/clever-tutorial/ic798984.png "direkt inloggning")

    > [!NOTE]
    > Innan du kan testa enkel inloggning, du måste kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) kan aktivera SSO för Office 365 i serverdelen.

1. På den **direkt inloggning** utför följande steg:
    
      ![Direkt inloggning](./media/clever-tutorial/ic798985.png "direkt inloggning")
    
      a. Skriv den **inloggnings-URL**.
    
      >[!NOTE]
      >Den **inloggnings-URL** är ett anpassat värde. Kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) att hämta det här värdet.
    
      b. Som **Identitetssystemet**väljer **ADFS**.

      c. I den **Metadata_url** textrutan klistra in **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.
    
      d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/clever-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/clever-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/clever-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/clever-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-clever-test-user"></a>Skapa en smarta testanvändare

Om du vill aktivera Azure AD-användare att logga in på Clever, måste de etableras i Clever.

När det gäller Clever, arbeta med [smarta klienten supportteamet](https://clever.com/about/contact/) att lägga till användare i smarta-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

>[!NOTE]
>Du kan använda alla andra smarta användare konto verktyg för att skapa eller API: er som tillhandahålls av Clever att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Clever.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Clever, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Clever**.

    ![Clever länken i listan med program](./media/clever-tutorial/tutorial_clever_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen smarta i åtkomstpanelen, du bör få automatiskt loggat in på ditt smarta program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

