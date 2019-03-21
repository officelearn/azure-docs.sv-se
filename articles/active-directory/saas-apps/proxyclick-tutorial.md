---
title: 'Självstudier: Azure Active Directory-integrering med Proxyclick | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5ce628716e9e8d0094f678c3d67dcfad9e24ada
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860541"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudier: Azure Active Directory-integrering med Proxyclick

I den här självstudien får du lära dig hur du integrerar Proxyclick med Azure Active Directory (AD Azure).

Integrera Proxyclick med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Proxyclick.
- Du kan aktivera användarna att automatiskt få loggat in på Proxyclick (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Proxyclick, behöver du följande objekt:

- En Azure AD-prenumeration
- En Proxyclick enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Proxyclick från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-proxyclick-from-the-gallery"></a>Att lägga till Proxyclick från galleriet
För att konfigurera integrering av Proxyclick i Azure AD, som du behöver lägga till Proxyclick från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Proxyclick från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Proxyclick**väljer **Proxyclick** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Proxyclick i resultatlistan](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Proxyclick baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Proxyclick är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Proxyclick upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Proxyclick, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Proxyclick](#create-a-proxyclick-test-user)**  – du har en motsvarighet för Britta Simon i Proxyclick som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Proxyclick program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Proxyclick:**

1. I Azure-portalen på den **Proxyclick** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. På den **Proxyclick domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Proxyclick domän och URL: er med enkel inloggning för information](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://saml.proxyclick.com/consume/<companyId>`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Proxyclick domän och URL: er med enkel inloggning för information](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med de faktiska identifierare, svars-URL och inloggnings-URL, vilket beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/proxyclick-tutorial/tutorial_general_400.png)

1. På den **Proxyclick Configuration** klickar du på **konfigurera Proxyclick** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. I ett annat webbläsarfönster logga du in på webbplatsen Proxyclick företag som administratör.

1. Välj **konto och inställningar**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure1.png)

1. Rulla ned till den **INTEGRERINGAR** och välj **SAML**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure2.png)

1. I den **SAML** avsnittet, utför följande steg:

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure3.png)

    a. Kopiera **URL för SAML-konsument** värde och klistra in den i **svars-URL** -textrutan i **Proxyclick domän och URL: er** avsnittet på Azure-portalen.

    b. Kopiera **omdirigerings-URL för SAML SSO** värde och klistra in den i **inloggnings-URL** och **identifierare** textrutor i **Proxyclick domän och URL: er** avsnittet på Azure-portalen.

    c. Välj **metod för SAML-begäran** som **omdirigering för HTTP**.

    d. I den **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** värde, som du har kopierat från Azure-portalen.

    e. I den **slutpunkts-URL för SAML 2.0** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL** kopieras från Azure-portalen.

    f. Öppna filen nedladdade certifikatet från Azure-portalen i anteckningar och klistra in den i den **certifikat** textrutan.

    g. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/proxyclick-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-proxyclick-test-user"></a>Skapa en Proxyclick testanvändare

Om du vill aktivera Azure AD-användare att logga in på Proxyclick, måste de etableras i Proxyclick. När det gäller Proxyclick är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Proxyclick företag som administratör.

1. Klicka på **kollegor** från det övre navigeringsfältet.

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user1.png)

1. Klicka på **lägga till en kollega**

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user2.png)

1. I den **lägga till en kollega** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user3.png)

    a. I den **e-post** textrutan typ e-postadressen för användaren som **brittasimon\@contoso.com**.

    b. I textrutan **Förnamn** skriver du förnamnet på användaren som Britta.

    c. I textrutan **Efternamn** skriver du efternamnet som Simon.

    d. Klicka på **Lägg till användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Proxyclick.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Proxyclick, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Proxyclick**.

    ![Länken Proxyclick i listan med program](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Proxyclick i åtkomstpanelen du bör få automatiskt loggat in på ditt Proxyclick program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

