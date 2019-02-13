---
title: 'Självstudier: Azure Active Directory-integrering med SecureW2 JoinNow Connector | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SecureW2 JoinNow Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175327"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Självstudier: Azure Active Directory-integrering med SecureW2 JoinNow Connector

I den här självstudien får du lära dig hur du integrerar SecureW2 JoinNow anslutningen med Azure Active Directory (AD Azure).

Integrera SecureW2 JoinNow anslutningen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SecureW2 JoinNow Connector.
- Du kan aktivera användarna att automatiskt få loggat in på SecureW2 JoinNow Connector (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SecureW2 JoinNow Connector, behöver du följande objekt:

- En Azure AD-prenumeration
- En SecureW2 JoinNow enkel inloggning aktiverat för produktanslutning

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SecureW2 JoinNow Connector från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Att lägga till SecureW2 JoinNow Connector från galleriet
Om du vill konfigurera integreringen av SecureW2 JoinNow anslutningen till Azure AD, som du behöver lägga till SecureW2 JoinNow Connector från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SecureW2 JoinNow Connector från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SecureW2 JoinNow Connector**väljer **SecureW2 JoinNow Connector** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SecureW2 JoinNow anslutningen i listan med resultat](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SecureW2 JoinNow Anslutningsapp baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i SecureW2 JoinNow anslutning till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SecureW2 JoinNow anslutningen ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SecureW2 JoinNow Connector, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare SecureW2 JoinNow Connector](#create-a-securew2-joinnow-connector-test-user)**  – du har en motsvarighet för Britta Simon i SecureW2 JoinNow koppling som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SecureW2 JoinNow anslutningsprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SecureW2 JoinNow Connector:**

1. I Azure-portalen på den **SecureW2 JoinNow Connector** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. På den **SecureW2 JoinNow Connector domän och URL: er** avsnittet, utför följande steg:

    ![SecureW2 JoinNow Connector domän och URL: er med enkel inloggning för information](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [SecureW2 JoinNow Connector-klient supportteamet](mailto:support@securew2.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **SecureW2 JoinNow Connector** sida, som du behöver skicka de hämtade **XML-Metadata för** till [SecureW2 JoinNow Connector supportteamet](mailto:support@securew2.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Skapa en testanvändare SecureW2 JoinNow Connector

I det här avsnittet skapar du en användare som kallas Britta Simon i SecureW2 JoinNow Connector. Arbeta med [SecureW2 JoinNow Connector-klient supportteamet](mailto:support@securew2.com) att lägga till användare i SecureW2 JoinNow Connector-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SecureW2 JoinNow anslutningstjänsten.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon SecureW2 JoinNow Connector, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SecureW2 JoinNow Connector**.

    ![SecureW2 JoinNow Connector-länk i listan med program](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

**Om du vill testa programmet utför du följande steg:** 

a. Öppna SecureW2 JoinNow Connector-klient, Välj lämplig enheten i listan och klicka på **logga In** knappen.

b. Standardwebbläsare får öppnas och du bör vara till Azure-portalen för autentisering.

c. Det ska gå tillbaka till första landningssidan för SecureW2 JoinNow koppling på lyckad autentisering.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

