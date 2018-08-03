---
title: 'Självstudier: Azure Active Directory-integration med PagerDuty | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 0e571880d9893c0027c200c6f49dc704fea09ead
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443706"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Självstudier: Azure Active Directory-integration med PagerDuty

I den här självstudien får du lära dig hur du integrerar PagerDuty med Azure Active Directory (AD Azure).

Integrera PagerDuty med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PagerDuty
- Du kan aktivera användarna att automatiskt få loggat in på PagerDuty (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PagerDuty, behöver du följande objekt:

- En Azure AD-prenumeration
- En PagerDuty enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till PagerDuty från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pagerduty-from-the-gallery"></a>Att lägga till PagerDuty från galleriet
För att konfigurera integrering av PagerDuty i Azure AD, som du behöver lägga till PagerDuty från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PagerDuty från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **PagerDuty**väljer **PagerDuty** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PagerDuty baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i PagerDuty är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i PagerDuty upprättas.

I PagerDuty, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med PagerDuty, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en PagerDuty-testanvändare](#create-a-pagerduty-test-user)**  – du har en motsvarighet för Britta Simon i PagerDuty som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PagerDuty-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PagerDuty:**

1. I Azure-portalen på den **PagerDuty** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

1. På den **PagerDuty domän och URL: er** avsnittet, utför följande steg:

    ![PagerDuty domän och URL: er med enkel inloggning för information](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [PagerDuty klienten supportteamet](https://www.pagerduty.com/support/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/pagerduty-tutorial/tutorial_general_400.png)

1. På den **PagerDuty Configuration** klickar du på **konfigurera PagerDuty** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![PagerDuty-konfiguration](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

1. Logga in på webbplatsen Pagerduty företag som en administratör i ett annat webbläsarfönster.

1. Klicka på menyn längst upp **kontoinställningar**.

    ![Kontoinställningar](./media/pagerduty-tutorial/ic778535.png "kontoinställningar")

1. Klicka på **enkel inloggning**.

    ![Enkel inloggning](./media/pagerduty-tutorial/ic778536.png "enkel inloggning")

1. På den **aktivera enkel inloggning (SSO)** utför följande steg:

    ![Aktivera enkel inloggning](./media/pagerduty-tutorial/ic778537.png "aktivera enkel inloggning")

    a. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan
  
    b. I den **inloggnings-URL** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
  
    c. I den **URL för utloggning** textrutan klistra in **URL: en för utloggning** som du har kopierat från Azure-portalen.

    d. Välj **inloggningen för Tillåt användarnamn/lösenord**.

    e. Välj **kräver exakt autentisering kontext jämförelse** kryssrutan.

    f. Klicka på **spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/pagerduty-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/pagerduty-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/pagerduty-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pagerduty-test-user"></a>Skapa en PagerDuty-testanvändare

Om du vill aktivera Azure AD-användare att logga in på PagerDuty, måste de etableras i PagerDuty.  
När det gäller PagerDuty är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Pagerduty användare konto verktyg för att skapa eller API: er som tillhandahålls av Pagerduty för att etablera Azure Active Directory användarkonton.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Pagerduty** klient.

1. Klicka på menyn längst upp **användare**.

1. Klicka på **lägga till användare**.
   
    ![Lägga till användare](./media/pagerduty-tutorial/ic778539.png "lägga till användare")

1.  På den **bjuda in ditt team** dialogrutan utför följande steg:
   
    ![Bjud in ditt team](./media/pagerduty-tutorial/ic778540.png "bjuda in ditt team")

    a. Skriv den **första och sista namnet** för användare som **Britta Simon**. 
   
    b. Ange **e-post** -adressen för användaren som **brittasimon@contoso.com**.
   
    c. Klicka på **Lägg till**, och klicka sedan på **skicka inbjudningar**.
   
    >[!NOTE]
    >Alla tillagda användare får en inbjudan till att skapa en PagerDuty-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PagerDuty.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon PagerDuty, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **PagerDuty**.

    ![Länken PagerDuty i listan med program](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PagerDuty åtkomst Panelyou bör hämta automatiskt loggat in på ditt PagerDuty-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
