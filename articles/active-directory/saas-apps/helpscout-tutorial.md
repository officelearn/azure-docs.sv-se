---
title: 'Självstudier: Azure Active Directory-integration med hjälpa Scout | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hjälpa Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 0bbdf576c38207349bb45e7b54f3ffc85ecf3d36
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449442"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Självstudier: Azure Active Directory-integration med hjälpa Scout

Lär dig hur du integrerar hjälpa Scout med Azure Active Directory (AD Azure) i den här självstudien.

Integrera hjälpa Scout med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till att Scout.
- Du kan aktivera användarna att automatiskt få loggat in på att Scout (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med hjälpa Scout behöver du följande objekt:

- En Azure AD-prenumeration
- En hjälpa Scout enkel inloggning aktiverad prenumeration

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till att Scout från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-help-scout-from-the-gallery"></a>Att lägga till att Scout från galleriet
För att konfigurera integrering av hjälpa Scout i Azure AD, som du behöver lägga till att Scout från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till att Scout från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **hjälpa Scout**väljer **hjälpa Scout** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Hjälp Scout i resultatlistan](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med hjälpa Scout baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i hjälpa Scout är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i hjälpa Scout upprättas.

Hjälp Scout använder e-postadresser för inloggningar, så Använd samma för att upprätta länken relationen, **e-postadress** som **användarnamn** i Azure AD.

Om du vill konfigurera och testa Azure AD enkel inloggning med hjälpa Scout, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare hjälpa Scout](#create-a-help-scout-test-user)**  – du har en motsvarighet för Britta Simon i hjälpa Scout som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för att Scout.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hjälpa Scout:**

1. I Azure-portalen på den **hjälpa Scout** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

1. På den **hjälpa Scout domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Hjälpinformation Scout domän och URL: er enkel inloggning](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identifieraren** är den **”målgrupp URI (Service Provider entitets-ID)”** från att Scout börjar med `urn:`

    b. **Svars-URL** är den **”efter tillbaka URL (försäkran URL för Konsumenttjänst)”** från att Scout börjar med `https://` 

    > [!NOTE] 
    > Värdena i dessa URL: er är bara exempel. Du måste uppdatera dessa värden från faktiska svars-URL och identifierare. Du får dessa värden från den **enkel inloggning** fliken under avsnittet för autentisering, som beskrivs senare i självstudien.

1. Om du vill konfigurera programmet i **SP** initierad läge, kontrollera **visa avancerade URL-inställningar** och utföra följande steg:

    ![Hjälpinformation Scout domän och URL: er enkel inloggning](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://secure.helpscout.net/members/login/`
     
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/helpscout-tutorial/tutorial_general_400.png)


1. På den **hjälpa Scout Configuration** klickar du på **konfigurera hjälpa Scout** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet**.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/config.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen för företaget att Scout som administratör.

1. När du är inloggad i klickar du på **”hantera”** från den översta menyn och välj sedan **”Company”** från den nedrullningsbara menyn.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings1.png) 
 
1. Välj **”autentisering”** på den vänstra menyn. 

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings2.png) 

1. Detta tar dig till avsnittet för SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings3.png) 
 
    a. Kopiera den **efter tillbaka URL (försäkran URL för Konsumenttjänst)** och klistra in värdet i den **svars-URL** rutan i Azure-portalen under hjälpa Scout **domän och URL: er** avsnittet.
    
    b. Kopiera den **Målgrupps-URI (Service Provider entitets-ID)** och klistra in värdet i den **identifierare** rutan i Azure-portalen under hjälpa Scout **domän och URL: er** avsnittet.

1. Visa/dölj **aktivera SAML** på och utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings4.png) 
 
    a. I **URL för enkel inloggning** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.
    
    b. Klicka på **överför certifikat** att ladda upp den **Certificate(Base64)** hämtas från Azure-portalen.

    c. Ange din organisations e-domänerna t.ex. - `contoso.com` i den **e-postdomäner** textrutan. Du kan avgränsa flera domäner med ett kommatecken. När som helst en hjälpa Scout användare eller administratör som registrerar den specifika domänen på den [Scout för att logga in sidan](https://secure.helpscout.net/members/login/) kommer att dirigeras till identitetsleverantör för autentisering med sina autentiseringsuppgifter.

    d. Slutligen kan du växla **kraft SAML inloggning** om du vill att användarna bara logga in på att Scout via via den här metoden. Om du vill fortfarande lämna alternativet att logga in med sina autentiseringsuppgifter för att Scout, kan du lämna den avstängd. Även om detta är aktiverat kommer en kontoinnehavare alltid att kunna logga in på att Scout med sina kontolösenord.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/helpscout-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/helpscout-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/helpscout-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/helpscout-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-help-scout-test-user"></a>Skapa en testanvändare hjälpa Scout

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i hjälpa Scout. Hjälp Scout stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. Om en användare inte redan finns i Hjälp Scout, skapas en ny när du försöker komma åt att Scout.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till att Scout.

![Tilldela rollen][200] 

**Om du vill tilldela hjälpa Scout Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **hjälpa Scout**.

    ![Länken Hjälp Scout i listan med program](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen hjälpa Scout i åtkomstpanelen du bör få automatiskt loggat in på ditt program för att Scout.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png

