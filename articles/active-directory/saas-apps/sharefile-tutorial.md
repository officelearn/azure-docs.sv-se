---
title: 'Självstudier: Azure Active Directory-integration med Citrix ShareFile | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9919be128ae651b589a37f957cc59ce6d171143f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431740"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Självstudier: Azure Active Directory-integration med Citrix ShareFile

Lär dig hur du integrerar Citrix ShareFile med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Citrix ShareFile med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Citrix ShareFile.
- Du kan aktivera användarna att automatiskt få loggat in på Citrix ShareFile (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Citrix ShareFile, behöver du följande objekt:

- En Azure AD-prenumeration
- En Citrix ShareFile enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Citrix ShareFile från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-citrix-sharefile-from-the-gallery"></a>Lägg till Citrix ShareFile från galleriet
För att konfigurera integrering av Citrix ShareFile i Azure AD, som du behöver lägga till Citrix ShareFile från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Citrix ShareFile från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Citrix ShareFile**väljer **Citrix ShareFile** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Citrix ShareFile i resultatlistan](./media/sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Citrix ShareFile utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Citrix ShareFile är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Citrix ShareFile upprättas.

I Citrix ShareFile tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Citrix ShareFile, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  – du har en motsvarighet för Britta Simon i Citrix ShareFile som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Citrix ShareFile-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Citrix ShareFile:**

1. I Azure-portalen på den **Citrix ShareFile** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/sharefile-tutorial/tutorial_sharefile_samlbase.png)

1. På den **Citrix ShareFile domän och URL: er** avsnittet, utför följande steg:

    ![Citrix ShareFile domän och URL: er med enkel inloggning för information](./media/sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.sharefile.com/saml/login`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [Citrix ShareFile klienten supportteamet](https://www.citrix.co.in/products/sharefile/support.html) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/sharefile-tutorial/tutorial_sharefile_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/sharefile-tutorial/tutorial_general_400.png)

1. På den **Citrix ShareFile Configuration** klickar du på **konfigurera Citrix ShareFile** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Citrix ShareFile konfiguration](./media/sharefile-tutorial/tutorial_sharefile_configure.png)

1. I ett annat webbläsarfönster, loggar du in på ditt **Citrix ShareFile** företagets plats som administratör.

1. I verktygsfältet högst upp, klickar du på **Admin**.

1. I det vänstra navigeringsfönstret väljer **Konfigurera enkel inloggning**.
   
    ![Kontot Administration](./media/sharefile-tutorial/ic773627.png "konto Administration")

1. På den **enkel inloggning / SAML 2.0-konfiguration** dialogrutan sidan under **grundinställningar**, utför följande steg:
   
    ![Enkel inloggning](./media/sharefile-tutorial/ic773628.png "enkel inloggning")
   
    a. Klicka på **aktivera SAML**.
    
    b. I **Your IDP-utfärdare / entitets-ID** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.

    c. Klicka på **ändra** bredvid den **X.509-certifikat** fältet och sedan ladda upp det certifikat som du laddade ned från Azure-portalen.
    
    d. I **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    e. I **URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

1. Klicka på **spara** på Citrix ShareFile-hanteringsportalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sharefile-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/sharefile-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sharefile-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/sharefile-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Skapa en Citrix ShareFile testanvändare

För att aktivera Azure AD-användare att logga in på Citrix ShareFile, måste de etableras i Citrix ShareFile. När det gäller Citrix ShareFile är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Citrix ShareFile** klient.

1. Klicka på **hantera användare \> hantera användare Home \> + skapa medarbetare**.
   
   ![Skapa medarbetare](./media/sharefile-tutorial/IC781050.png "skapa medarbetare")

1. På den **basinformation** avsnittet, utföra stegen nedan:
   
   ![Grundläggande Information](./media/sharefile-tutorial/IC799951.png "grundläggande Information")
   
   a. I den **e-postadress** textrutan skriver du e-postadressen för Britta Simon som **brittasimon@contoso.com**.
   
   b. I den **Förnamn** textrutan typ **Förnamn** för användare som **Britta**.
   
   c. I den **efternamn** textrutan typ **efternamn** för användare som **Simon**.

1. Klicka på **lägga till användare**.
  
   >[!NOTE]
   >Azure AD-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv. Du kan använda andra verktyg för Citrix ShareFile att skapa användaren-konto eller API: er som tillhandahålls av Citrix ShareFile för att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Citrix ShareFile.

![Tilldela rollen][200] 

**Om du vill tilldela Citrix ShareFile Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Citrix ShareFile**.

    ![Citrix ShareFile länken i listan med program](./media/sharefile-tutorial/tutorial_sharefile_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Citrix ShareFile i åtkomstpanelen du bör få automatiskt loggat in på ditt Citrix ShareFile-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharefile-tutorial/tutorial_general_01.png
[2]: ./media/sharefile-tutorial/tutorial_general_02.png
[3]: ./media/sharefile-tutorial/tutorial_general_03.png
[4]: ./media/sharefile-tutorial/tutorial_general_04.png

[100]: ./media/sharefile-tutorial/tutorial_general_100.png

[200]: ./media/sharefile-tutorial/tutorial_general_200.png
[201]: ./media/sharefile-tutorial/tutorial_general_201.png
[202]: ./media/sharefile-tutorial/tutorial_general_202.png
[203]: ./media/sharefile-tutorial/tutorial_general_203.png
