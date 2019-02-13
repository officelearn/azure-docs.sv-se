---
title: 'Självstudier: Azure Active Directory-integrering med Skyhigh nätverk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Skyhigh nätverk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5469241bd285c60711d6e47f40bc4f5d2bf336d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178506"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Självstudier: Azure Active Directory-integrering med Skyhigh nätverk

I den här självstudien får du lära dig hur du integrerar Skyhigh nätverk med Azure Active Directory (AD Azure).

Integrera Skyhigh nätverk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Skyhigh nätverk.
- Du kan aktivera användarna att automatiskt få loggat in på Skyhigh nätverk (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Skyhigh nätverk, behöver du följande objekt:

- En Azure AD-prenumeration
- En Skyhigh nätverk enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Skyhigh nätverk från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Att lägga till Skyhigh nätverk från galleriet
Om du vill konfigurera integreringen av Skyhigh nätverk till Azure AD, som du behöver lägga till Skyhigh nätverk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Skyhigh nätverk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Skyhigh nätverk**väljer **Skyhigh nätverk** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Skyhigh nätverk i listan med resultat](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Skyhigh nätverk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Skyhigh nätverk är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Skyhigh nätverk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Skyhigh nätverk, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Skyhigh nätverk](#create-a-skyhigh-networks-test-user)**  – du har en motsvarighet för Britta Simon i Skyhigh nätverk som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Skyhigh nätverk.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Skyhigh nätverk:**

1. I Azure-portalen på den **Skyhigh nätverk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

1. På den **Skyhigh nätverk domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Skyhigh nätverk domän och URL: er med enkel inloggning för information](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Skyhigh nätverk domän och URL: er med enkel inloggning för information](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Skyhigh nätverk klienten supportteamet](mailto:support@skyhighnetworks.com) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
1. På den **Skyhigh nätverk Configuration** klickar du på **konfigurera Skyhigh nätverk** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Skyhigh nätverk konfiguration](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

1. Att konfigurera enkel inloggning på **Skyhigh nätverk** sida, som du behöver skicka de hämtade **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [ Skyhigh nätverk supportteam](mailto:support@skyhighnetworks.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Skapa en testanvändare Skyhigh nätverk

I det här avsnittet skapar du en användare som kallas Britta Simon i Skyhigh nätverk. Arbeta med [Skyhigh nätverk supportteam](mailto:support@skyhighnetworks.com) att lägga till användare i Skyhigh nätverk-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Skyhigh nätverk.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Skyhigh nätverk, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Skyhigh nätverk**.

    ![Länken Skyhigh nätverk i listan med program](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Skyhigh nätverk i åtkomstpanelen du bör få automatiskt loggat in på programmets Skyhigh nätverk.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png

