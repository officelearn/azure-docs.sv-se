---
title: 'Självstudier: Azure Active Directory-integrering med Reviewsnap | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Reviewsnap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b00fb373-2b31-4dcf-84ce-abc29e4c639c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8fd84618da13693ac8edacf2a1d817691843b8f7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966588"
---
# <a name="tutorial-azure-active-directory-integration-with-reviewsnap"></a>Självstudier: Azure Active Directory-integrering med Reviewsnap

I kursen får lära du att integrera Reviewsnap med Azure Active Directory (AD Azure).

Integrera Reviewsnap med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Reviewsnap.
- Du kan aktivera användarna att automatiskt hämta loggat in på Reviewsnap (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Reviewsnap, behöver du följande:

- En Azure AD-prenumeration
- En Reviewsnap enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Reviewsnap från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-reviewsnap-from-the-gallery"></a>Att lägga till Reviewsnap från galleriet
Du måste lägga till Reviewsnap från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Reviewsnap i Azure AD.

**Utför följande steg för att lägga till Reviewsnap från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Reviewsnap**väljer **Reviewsnap** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Reviewsnap i resultatlistan](./media/reviewsnap-tutorial/tutorial_reviewsnap_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Reviewsnap baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Reviewsnap motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Reviewsnap upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Reviewsnap, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Reviewsnap](#create-a-reviewsnap-test-user)**  – du har en motsvarighet för Britta Simon i Reviewsnap som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Reviewsnap program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Reviewsnap:**

1. I Azure-portalen på den **Reviewsnap** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/reviewsnap-tutorial/tutorial_reviewsnap_samlbase.png)

3. På den **Reviewsnap domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Reviewsnap domän med enkel inloggning information](./media/reviewsnap-tutorial/tutorial_reviewsnap_url.png)

    a. I den **identifierare** textruta, ange ett URL-Adressen: `https://app.reviewsnap.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://app.reviewsnap.com/auth/saml/callback?namespace=<CUSTOMER_NAMESPACE>
`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Reviewsnap domän med enkel inloggning information](./media/reviewsnap-tutorial/tutorial_reviewsnap_url1.png)

    I den **inloggnings-URL** textruta, ange ett URL-Adressen: `https://app.reviewsnap.com/login`
     
    > [!NOTE] 
    > Reply URL-värdet är inte verkliga. Uppdatera värdet med det faktiska Reply-URL. Kontakta [Reviewsnap klienten supportteamet](mailto:support@reviewsnap.com) värdet hämtas. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/reviewsnap-tutorial/tutorial_reviewsnap_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/reviewsnap-tutorial/tutorial_general_400.png)
    
7. På den **Reviewsnap Configuration** klickar du på **konfigurera Reviewsnap** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Reviewsnap konfiguration](./media/reviewsnap-tutorial/tutorial_reviewsnap_configure.png) 

8. Konfigurera enkel inloggning på **Reviewsnap** sida, måste du skicka den hämtade **certifikat (Base64), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [Reviewsnap supportteam](mailto:support@reviewsnap.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/reviewsnap-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/reviewsnap-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/reviewsnap-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/reviewsnap-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-reviewsnap-test-user"></a>Skapa en testanvändare Reviewsnap

I det här avsnittet skapar du en användare som kallas Britta Simon i Reviewsnap. Arbeta med [Reviewsnap supportteamet](mailto:support@reviewsnap.com) att lägga till användare i Reviewsnap-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Reviewsnap.

![Tilldela rollen][200] 

**Om du vill tilldela Reviewsnap Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Reviewsnap**.

    ![Länken Reviewsnap i listan med program](./media/reviewsnap-tutorial/tutorial_reviewsnap_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Reviewsnap på åtkomstpanelen du bör få automatiskt loggat in på ditt Reviewsnap program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/reviewsnap-tutorial/tutorial_general_01.png
[2]: ./media/reviewsnap-tutorial/tutorial_general_02.png
[3]: ./media/reviewsnap-tutorial/tutorial_general_03.png
[4]: ./media/reviewsnap-tutorial/tutorial_general_04.png

[100]: ./media/reviewsnap-tutorial/tutorial_general_100.png

[200]: ./media/reviewsnap-tutorial/tutorial_general_200.png
[201]: ./media/reviewsnap-tutorial/tutorial_general_201.png
[202]: ./media/reviewsnap-tutorial/tutorial_general_202.png
[203]: ./media/reviewsnap-tutorial/tutorial_general_203.png

