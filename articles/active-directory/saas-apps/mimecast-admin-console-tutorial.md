---
title: 'Självstudier: Azure Active Directory-integration med Mimecast administratörskonsolen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast-administratörskonsolen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: ce4142c5b4a20886a94c87699f262f7238fc2cb4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438577"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Självstudier: Azure Active Directory-integration med Mimecast-administratörskonsolen

I den här självstudien får du lära dig hur du integrerar Mimecast-administratörskonsolen med Azure Active Directory (AD Azure).

Integrera Mimecast-administratörskonsolen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mimecast-administratörskonsolen.
- Du kan aktivera användarna att automatiskt få loggat in på administratörskonsolen för Mimecast (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mimecast-administratörskonsolen, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mimecast administratörskonsolen enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mimecast-administratörskonsolen från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Att lägga till Mimecast-administratörskonsolen från galleriet
Om du vill konfigurera integreringen av Mimecast administrationskonsolen till Azure AD, som du behöver lägga till Mimecast-administratörskonsolen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mimecast-administratörskonsolen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Mimecast administratörskonsolen**väljer **Mimecast administratörskonsolen** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Mimecast administratörskonsolen i resultatlistan](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mimecast administratörskonsolen baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i administratörskonsolen för Mimecast är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mimecast administratörskonsolen upprättas.

I administratörskonsolen för Mimecast tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mimecast-administratörskonsolen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Mimecast administratörskonsolen](#create-a-mimecast-admin-console-test-user)**  – du har en motsvarighet för Britta Simon i administratörskonsolen för Mimecast som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Mimecast-administratörskonsolen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mimecast administratörskonsolen:**

1. I Azure-portalen på den **Mimecast administratörskonsolen** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

1. På den **Mimecast Admin konsolens domän och URL: er** avsnittet, utför följande steg:

    ![Mimecast Admin konsolens domän och URL: er med enkel inloggning för information](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    I den **inloggnings-URL** textrutan anger du URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > URL: en inloggning beror på region.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mimecast-admin-console-tutorial/tutorial_general_400.png)

1. På den **Mimecast Administratörskonfigurationen för konsolen** klickar du på **konfigurera Mimecast administratörskonsolen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Mimecast Administratörskonfigurationen-konsolen](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

1. Logga in på den Mimecast administratörskonsolen som en administratör i ett annat webbläsarfönster.

1. Gå till **Services \> program**.

    ![Tjänster](./media/mimecast-admin-console-tutorial/ic794998.png "tjänster")

1. Klicka på **autentisering profiler**.

    ![Autentisering profiler](./media/mimecast-admin-console-tutorial/ic794999.png "autentisering profiler")
    
1. Klicka på **ny autentisering profil**.

    ![Ny autentisering profiler](./media/mimecast-admin-console-tutorial/ic795000.png "nya autentisering-profiler")

1. I den **autentisering profil** avsnittet, utför följande steg:

    ![Autentisering profil](./media/mimecast-admin-console-tutorial/ic795015.png "autentisering profil")
    
    a. I den **beskrivning** textrutan anger du ett namn för din konfiguration.
    
    b. Välj **framtvinga SAML-autentisering för Mimecast administratörskonsolen**.
    
    c. Som **Provider**väljer **Azure Active Directory**.
    
    d. Klistra in **SAML entitets-ID**, som du har kopierat från Azure-portalen till den **utfärdar-URL** textrutan.
    
    e. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **inloggnings-URL** textrutan.

    f. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **URL för utloggning** textrutan.
    
    >[!NOTE]
    >Inloggnings-URL-värdet och utloggnings-URL-värdet är administrationskonsolen Mimecast samma.
    
    g. Öppna ditt Base64-certifikat som hämtats från Azure-portalen i anteckningar, ta bort den första raden (”*--*”) och den sista raden (”*--*”), kopiera återstående innehåll på den i din Urklipp och klistra in den till den **identitetscertifikat Provider (Metadata)** textrutan.
    
    h. Välj **tillåta enkel inloggning på**.
    
    i. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mimecast-admin-console-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mimecast-admin-console-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mimecast-admin-console-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Skapa en testanvändare Mimecast-administratörskonsolen

För att aktivera Azure AD-användare att logga in på administratörskonsolen för Mimecast, måste de etableras i Mimecast-administratörskonsolen. När det gäller Mimecast administratörskonsolen är etablering en manuell aktivitet.

* Du måste registrera en domän innan du kan skapa användare.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Mimecast administratörskonsolen** som administratör.
1. Gå till **kataloger \> interna**.
   
   ![Kataloger](./media/mimecast-admin-console-tutorial/ic795003.png "kataloger")
1. Klicka på **registrera ny domän**.
   
   ![Registrera ny domän](./media/mimecast-admin-console-tutorial/ic795004.png "registrera ny domän")
1. När du har skapat den nya domänen, klickar du på **nya adressen**.
   
   ![Ny adress](./media/mimecast-admin-console-tutorial/ic795005.png "ny adress")
1. I dialogrutan Ny adress utför du följande steg:
   
   ![Spara](./media/mimecast-admin-console-tutorial/ic795006.png "spara")
   
   a. Skriv den **e-postadress**, **globalt namn**, **lösenord**, och **Bekräfta lösenord** attribut för en giltig Azure AD-konto som du vill etablera i den relaterade textrutor.

   b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda ytterligare Mimecast administratörskonsolen användaren-konto skapas verktyg eller API: er som tillhandahålls av Mimecast-administratörskonsolen för att etablera användarkonton i Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Mimecast-administrationskonsolen.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Mimecast-administratörskonsolen, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Mimecast administratörskonsolen**.

    ![Länken Mimecast administratörskonsolen i listan med program](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mimecast administratörskonsolen i åtkomstpanelen du bör få automatiskt loggat in på programmets Mimecast-administratörskonsolen.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-admin-console-tutorial/tutorial_general_203.png

