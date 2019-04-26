---
title: 'Självstudier: Azure Active Directory-integrering med Leapsome | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260084"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Självstudier: Azure Active Directory-integrering med Leapsome

I den här självstudien får du lära dig hur du integrerar Leapsome med Azure Active Directory (AD Azure).

Integrera Leapsome med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Leapsome.
- Du kan aktivera användarna att automatiskt få loggat in på Leapsome (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Leapsome, behöver du följande objekt:

- En Azure AD-prenumeration
- En Leapsome enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Leapsome från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-leapsome-from-the-gallery"></a>Att lägga till Leapsome från galleriet
För att konfigurera integrering av Leapsome i Azure AD, som du behöver lägga till Leapsome från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Leapsome från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Leapsome**väljer **Leapsome** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Leapsome i resultatlistan](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Leapsome baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Leapsome är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Leapsome upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Leapsome, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Leapsome](#create-a-leapsome-test-user)**  – du har en motsvarighet för Britta Simon i Leapsome som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Leapsome program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Leapsome:**

1. I Azure-portalen på den **Leapsome** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. På den **Leapsome domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Leapsome domän och URL: er med enkel inloggning för information](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://www.leapsome.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Leapsome domän och URL: er med enkel inloggning för information](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Föregående svars-URL och inloggnings-URL-värdet är inte verkliga värdet. Uppdaterar du dem med de faktiska värdena som förklaras senare under kursen.

1. Leapsome program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel.
    
    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde | Namnområde |
    | ---------------| --------------- | --------- |   
    | förnamn | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | bild | Webbadressen till medarbetarens bild | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Värdet för bild-attributet är inte verkliga. Uppdatera det här värdet med URL: en för bilden. Att hämta värdet kontakten [Leapsome klienten supportteamet](mailto:support@leapsome.com).
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. I den **Namespace** textrutan skriver namnrymds-uri för den raden.
    
    e. Klicka på **Ok**

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. På den **Leapsome Configuration** klickar du på **konfigurera Leapsome** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Leapsome konfiguration](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. I ett annat webbläsarfönster, logga in på Leapsome som en administratör.

1. Klicka på inställningar logotyp längst upp till höger, och klicka sedan på **administratörsinställningar**. 

    ![Leapsome set](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Klicka på den vänstra menyraden **enkel inloggning (SSO)**, och på den **SAML-baserad enkel inloggning (SSO)** sidan utför följande steg:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Välj **aktivera SAML-baserad enkel inloggning**.

    b. Kopiera den **inloggnings-URL (peka användarna här om du vill börja logga in)** värde och klistra in den i den **inloggnings-URL** -textrutan i **Leapsome domän och URL: er** avsnittet på Azure-portalen.

    c. Kopiera den **svars-URL (får svar från din identitetsprovider)** värde och klistra in den i den **svars-URL** -textrutan i **Leapsome domän och URL: er** avsnittet på Azure-portalen.

    d. I den **inloggnings-URL för enkel inloggning (som anges av identitetsprovider)** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du kopierade från Azure-portalen.

    e. Kopiera certifikatet som du har hämtat från Azure-portalen utan--börjar certifikatet och END certifikat, kommentarer och klistra in den i den **certifikat (som anges av identitetsprovider)** textrutan.

    f. Klicka på **SSO UPPDATERINGSINSTÄLLNINGAR**.
    
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/leapsome-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/leapsome-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/leapsome-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/leapsome-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-leapsome-test-user"></a>Skapa en Leapsome testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Leapsome. Arbeta med [Leapsome klienten supportteamet](mailto:support@leapsome.com) att lägga till användare eller domän, som måste tillåtas i Leapsome-plattformen. Om domänen har lagts till av teamet, kommer användare får automatiskt tillhandahållas för Leapsome-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Leapsome.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Leapsome, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Leapsome**.

    ![Länken Leapsome i listan med program](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Leapsome i åtkomstpanelen du bör få automatiskt loggat in på ditt Leapsome program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

