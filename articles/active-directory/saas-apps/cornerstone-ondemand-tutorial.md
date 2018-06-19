---
title: 'Självstudier: Azure Active Directory-integrering med hörnstenarna OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hörnstenarna OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: jeedes
ms.openlocfilehash: 79ae29e8d922112ca5d351f331b841b74cec4cdd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963863"
---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Självstudier: Azure Active Directory-integrering med hörnstenarna OnDemand

I kursen får lära du att integrera hörnstenarna OnDemand med Azure Active Directory (AD Azure).

Integrera hörnstenarna OnDemand med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till hörnstenarna OnDemand
- Du kan aktivera användarna att automatiskt hämta loggat in på hörnstenarna OnDemand (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med hörnstenarna OnDemand behöver du följande:

- En Azure AD-prenumeration
- En hörnsten OnDemand enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till hörnstenarna OnDemand från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Att lägga till hörnstenarna OnDemand från galleriet
Du måste lägga till hörnstenarna OnDemand från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av hörnstenarna OnDemand i Azure AD.

**Utför följande steg för att lägga till hörnstenarna OnDemand från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **hörnstenarna OnDemand**.

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_search.png)

5. Välj i resultatpanelen **hörnstenarna OnDemand**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med hörnstenarna OnDemand baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i hörnstenarna OnDemand motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i hörnstenarna OnDemand upprättas.

I grunden OnDemand tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med hörnstenarna OnDemand, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare hörnstenarna OnDemand](#creating-a-cornerstone-ondemand-test-user)**  – har en motsvarighet för Britta Simon hörnstenarna OnDemand som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt hörnstenarna OnDemand-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hörnstenarna OnDemand:**

1. I Azure-portalen på den **hörnstenarna OnDemand** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_samlbase.png)

3. På den **hörnstenarna OnDemand domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<company>.csod.com`

    b. I **identifierare** textruta Skriv en URL med följande mönster: `https://<company>.csod.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [hörnstenarna OnDemand klienten supportteamet](mailTo:moreinfo@csod.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_general_400.png)

6. På den **hörnstenarna OnDemand Configuration** klickar du på **konfigurera hörnstenarna OnDemand** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_configure.png) 

7. Konfigurera enkel inloggning på **hörnstenarna OnDemand** sida, måste du skicka den hämtade **certifikat**, **Sign-Out URL** och **SAML enkel inloggning Tjänstwebbadress** till [hörnstenarna OnDemand supportteamet](mailTo:moreinfo@csod.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/create_aaduser_03.png)

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/cornerstone-ondemand-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-cornerstone-ondemand-test-user"></a>Skapa en OnDemand hörnstenarna testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i hörnstenarna OnDemand. Hörnstenarna OnDemand stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](cornerstone-ondemand-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt gör du följande:**

För att konfigurera användaretablering, skicka information (t.ex.: namn, e-post) om Azure AD-användare du vill etablera till den [hörnstenarna OnDemand supportteamet](mailTo:moreinfo@csod.com).

>[!NOTE]
>Du kan använda något annat hörnstenarna OnDemand användarens konto skapas verktyg eller API: er som tillhandahålls av hörnstenarna OnDemand etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till hörnstenarna OnDemand.

![Tilldela användare][200] 

**Om du vill tilldela hörnstenarna OnDemand Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **hörnstenarna OnDemand**.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen hörnstenarna OnDemand på åtkomstpanelen du bör få automatiskt loggat in på ditt hörnstenarna OnDemand-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](cornerstone-ondemand-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cornerstone-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/cornerstone-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/cornerstone-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/cornerstone-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/cornerstone-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/cornerstone-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/cornerstone-ondemand-tutorial/tutorial_general_203.png
