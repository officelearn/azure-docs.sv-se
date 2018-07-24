---
title: 'Självstudier: Azure Active Directory-integration med Bridgeline har delats upp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bridgeline har delats upp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b018472f-c8b3-403d-ae66-9ed26a35f413
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: c429afa12bc11db68d041fef96f66b3f4c7f0b1b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206131"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>Självstudier: Azure Active Directory-integration med Bridgeline har delats upp

I den här självstudien får du lära dig hur du integrerar Bridgeline har delats upp med Azure Active Directory (AD Azure).

Integrera Bridgeline har delats upp med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bridgeline har delats upp.
- Du kan aktivera användarna att automatiskt få loggat in på Bridgeline har delats upp (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bridgeline har delats upp, behöver du följande objekt:

- En Azure AD-prenumeration
- En Bridgeline har delats upp enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Bridgeline har delats upp från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>Att lägga till Bridgeline har delats upp från galleriet
För att konfigurera integrering av Bridgeline har delats upp i Azure AD, som du behöver lägga till Bridgeline har delats upp från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Bridgeline har delats upp från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Bridgeline har delats upp**väljer **Bridgeline har delats upp** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bridgeline har delats upp i resultatlistan](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bridgeline har delats upp baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användaren i Bridgeline har delats upp till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bridgeline har delats upp upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bridgeline har delats upp, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Bridgeline har delats upp testanvändare](#create-a-bridgeline-unbound-test-user)**  – du har en motsvarighet för Britta Simon i Bridgeline obundna som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bridgeline har delats upp program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bridgeline har delats upp:**

1. I Azure-portalen på den **Bridgeline har delats upp** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_samlbase.png)
 
3. På den **Bridgeline har delats upp domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Bridgeline domän och URL: er med enkel inloggning för information](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `iApps_UPSTT_<ENVIRONMENTNAME>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Bridgeline domän och URL: er med enkel inloggning för information](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Bridgeline har delats upp klienten supportteamet](mailto:support@iapps.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/bridgelineunbound-tutorial/tutorial_general_400.png)

6. På den **Bridgeline har delats upp konfigurationen** klickar du på **konfigurera Bridgeline obundna** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Bridgeline har delats upp konfigurationen](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_configure.png) 

7. Att konfigurera enkel inloggning på **Bridgeline har delats upp** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Bridgeline har delats upp supportteamet](mailto:support@iapps.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bridgelineunbound-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grobridgelineinbound**, och klicka sedan på **alla användare**.

    ![”Användare och grobridgelineinbound” och ”alla användare”-länkar](./media/bridgelineunbound-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bridgelineunbound-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bridgelineunbound-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bridgeline-unbound-test-user"></a>Skapa en Bridgeline har delats upp testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Bridgeline har delats upp. Bridgeline har delats upp stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Bridgeline har delats upp om den inte finns.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Bridgeline har delats upp supportteamet](mailto:support@iapps.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bridgeline har delats upp.

![Tilldela rollen][200] 

**Om du vill tilldela Bridgeline har delats upp Britta Simon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Bridgeline har delats upp**.

    ![Länken Bridgeline har delats upp i listan med program](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_app.png)  

3. I menyn till vänster, klickar du på **användare och grobridgelineinbound**.

    ![Länken ”användare och grobridgelineinbound”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grobridgelineinbound** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grobridgelineinbound** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grobridgelineinbound** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bridgeline har delats upp i åtkomstpanelen du bör få automatiskt loggat in på programmets Bridgeline har delats upp.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bridgelineunbound-tutorial/tutorial_general_01.png
[2]: ./media/bridgelineunbound-tutorial/tutorial_general_02.png
[3]: ./media/bridgelineunbound-tutorial/tutorial_general_03.png
[4]: ./media/bridgelineunbound-tutorial/tutorial_general_04.png

[100]: ./media/bridgelineunbound-tutorial/tutorial_general_100.png

[200]: ./media/bridgelineunbound-tutorial/tutorial_general_200.png
[201]: ./media/bridgelineunbound-tutorial/tutorial_general_201.png
[202]: ./media/bridgelineunbound-tutorial/tutorial_general_202.png
[203]: ./media/bridgelineunbound-tutorial/tutorial_general_203.png

