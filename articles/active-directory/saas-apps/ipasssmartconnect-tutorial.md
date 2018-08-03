---
title: 'Självstudier: Azure Active Directory-integration med iPass SmartConnect | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: ecfdd3fae1d394e3b57fcd325f44cad0d1a98534
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444902"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Självstudier: Azure Active Directory-integration med iPass SmartConnect

I den här självstudien får du lära dig hur du integrerar iPass SmartConnect med Azure Active Directory (AD Azure).

Integrera iPass SmartConnect med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iPass SmartConnect.
- Du kan aktivera användarna att automatiskt få loggat in på iPass SmartConnect (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iPass SmartConnect, behöver du följande objekt:

- En Azure AD-prenumeration
- En iPass SmartConnect enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till iPass SmartConnect från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Att lägga till iPass SmartConnect från galleriet
För att konfigurera integrering av iPass SmartConnect i Azure AD, som du behöver lägga till iPass SmartConnect från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iPass SmartConnect från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **iPass SmartConnect**väljer **iPass SmartConnect** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![iPass SmartConnect i resultatlistan](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i iPass SmartConnect är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i iPass SmartConnect måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för iPass SmartConnect](#create-an-ipass-smartconnect-test-user)**  – du har en motsvarighet för Britta Simon i iPass SmartConnect som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din iPass SmartConnect program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iPass SmartConnect:**

1. I Azure-portalen på den **iPass SmartConnect** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. På den **iPass SmartConnect domän och URL: er** om du vill konfigurera programmet i **IDP** initierad läge, behöver du inte utföra några steg.

    ![iPass SmartConnect domän och URL: er med enkel inloggning för information](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Kontrollera att visa avancerade URL-inställningar och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![iPass SmartConnect domän och URL: er med enkel inloggning för information](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Ange en URL i inloggnings-URL-textrutan: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. iPass SmartConnect program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/attribute.png)

1. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde | Namespace värde|
    | ---------------| --------------- |----------------|
    | Förnamn | User.givenName |   |
    | Efternamn | User.surname | |
    | e-post | User.userPrincipalName | |
    | användarnamn | User.userPrincipalName | |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Behåll namnområdesvärdet tom för den raden.

    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **iPass SmartConnect** sida, som du behöver skicka de hämtade **XML-Metadata för** och din **domännamn** till [iPass SmartConnect supportteam](mailto:help@ipass.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Skapa en iPass SmartConnect testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i iPass SmartConnect. Arbeta med [iPass SmartConnect supportteam](mailto:help@ipass.com) att lägga till användare eller domän som behövs för att bli godkänd i iPass SmartConnect-plattformen. Om domänen har lagts till av teamet, kommer användare får automatiskt tillhandahållas för iPass SmartConnect-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iPass SmartConnect.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon iPass SmartConnect, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **iPass SmartConnect**.

    ![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

**Om du vill testa programmet i SP-initierat flödet utför du följande steg:**

a. Ladda ned windows iPass SmartConnect klienten [här](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installera klienten och starta.

c. Klicka på **börjar**.

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Ange användarnamnet med domän. Klicka på **fortsätta**. Detta kommer att omdirigeras till inloggningssidan för Azure

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Aktivering av klienten kommer att startas efter en lyckad autentisering. Klienten ska aktiveras.

**Om du vill testa programmet i IdP-initierad flödet utför du följande steg:**

a. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klicka på iPass SmartConnect app.

c. Startar SSA sidan, klickar du på **ladda ned appen för Windows** installera iPass SmartConnect klienten.

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing4.png)

d. När installationen av klienten vid första starten kommer automatiskt startar aktivering när du godkänner villkoren.

e. Om aktiveringen inte startar, klicka på knappen Aktivera på SSA sidan för att påbörja aktiveringen.

f. Klienten ska aktiveras.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

