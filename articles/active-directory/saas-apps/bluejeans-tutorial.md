---
title: 'Självstudier: Azure Active Directory-integration med BlueJeans | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2ec94217a8df2efaa23eb3cc2c9d5a80e8037615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425995"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Självstudier: Azure Active Directory-integration med BlueJeans

I den här självstudien får du lära dig hur du integrerar BlueJeans med Azure Active Directory (AD Azure).

Integrera BlueJeans med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BlueJeans
- Du kan aktivera användarna att automatiskt få loggat in på BlueJeans (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med BlueJeans, behöver du följande objekt:

- En Azure AD-prenumeration
- En BlueJeans enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till BlueJeans från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bluejeans-from-the-gallery"></a>Att lägga till BlueJeans från galleriet
För att konfigurera integrering av BlueJeans i Azure AD, som du behöver lägga till BlueJeans från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BlueJeans från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **BlueJeans**.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

1. I resultatpanelen väljer **BlueJeans**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med BlueJeans baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i BlueJeans är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i BlueJeans upprättas.

I BlueJeans, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med BlueJeans, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare BlueJeans](#creating-a-bluejeans-test-user)**  – du har en motsvarighet för Britta Simon i BlueJeans som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BlueJeans program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BlueJeans:**

1. I Azure-portalen på den **BlueJeans** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

1. På den **BlueJeans domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.BlueJeans.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [BlueJeans klienten supportteamet](https://support.bluejeans.com/contact) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_general_400.png)

1. På den **BlueJeans Configuration** klickar du på **konfigurera BlueJeans** att öppna **konfigurera inloggning** fönster. Kopiera den **utloggning URL, ändra lösenord URL och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

1. Logga in på i ett annat webbläsarfönster din **BlueJeans** företagets plats som administratör.

1. Gå till **ADMIN \> gruppinställningar \> Security**.

   ![Administratören](./media/bluejeans-tutorial/IC785868.png "Admin")

1. I den **Security** avsnittet, utför följande steg:

   ![SAML enkel inloggning](./media/bluejeans-tutorial/IC785869.png "SAML enkel inloggning")

   a. Välj **SAML enkel inloggning**.

   b. Välj **aktivera automatisk etablering**.

1. Gå vidare med följande steg:

    ![Certifikat sökväg](./media/bluejeans-tutorial/IC785870.png "certifikat sökväg")

    a. Klicka på **Välj fil**, och sedan ladda upp det nedladdade certifikatet.

    b. Klistra in **SAML enkel inloggning för tjänst-URL** till den **inloggnings-URL** textrutan.

    c. Klistra in **ändra lösenord URL** till den **lösenordet ändra URL** textrutan.

    d. Klistra in **URL: en för utloggning** till den **URL för utloggning** textrutan.

1. Gå vidare med följande steg:

    ![Spara ändringar](./media/bluejeans-tutorial/IC785874.png "spara ändringar")

    a. I den **användar-id** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. I den **e-post** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klicka på **spara ändringar**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-bluejeans-test-user"></a>Skapa en BlueJeans testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i BlueJeans. BlueJeans stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](bluejeans-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på din **BlueJeans** företagets plats som administratör.

1. Gå till **ADMIN \> hantera användare \> lägga till användare**.

   ![Administratören](./media/bluejeans-tutorial/IC785877.png "Admin")

   >[!IMPORTANT]
   >Den **Lägg till användare** fliken är endast tillgänglig om i den **säkerhetsfliken**, **aktivera automatisk etablering** är avmarkerad. 

1. I den **Lägg till användare** avsnittet, utför följande steg:

    ![Lägg till användare](./media/bluejeans-tutorial/IC785886.png "lägga till användare")

    a. Ange ett **BlueJeans användarnamn**, en **e-postadress**, ett **BlueJeans möte ID**, ett **Moderator lösenord**, ett **fullständigt namn** , **företagets** på en giltig AAD-konto som du vill att etablera i den relaterade textrutor.

    b. Klicka på **lägga till användare**.

>[!NOTE]
>Du kan använda alla andra BlueJeans användare konto verktyg för att skapa eller API: er som tillhandahålls av BlueJeans att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BlueJeans.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon BlueJeans, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **BlueJeans**.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen BlueJeans i åtkomstpanelen, bör du få inloggningssidan i BlueJeans program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
