---
title: 'Självstudier: Azure Active Directory-integrering med moconavi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 032a674662fb2c55ee9a16b406418367ee7c797d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964000"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Självstudier: Azure Active Directory-integrering med moconavi

I kursen får lära du att integrera moconavi med Azure Active Directory (AD Azure).

Integrera moconavi med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till moconavi.
- Du kan aktivera användarna att automatiskt hämta loggat in på moconavi (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med moconavi, behöver du följande:

- En Azure AD-prenumeration
- En moconavi enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till moconavi från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-moconavi-from-the-gallery"></a>Att lägga till moconavi från galleriet
Du måste lägga till moconavi från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av moconavi i Azure AD.

**Utför följande steg för att lägga till moconavi från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **moconavi**väljer **moconavi** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![moconavi i resultatlistan](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med moconavi baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i moconavi motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i moconavi upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med moconavi, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare moconavi](#create-a-moconavi-test-user)**  – du har en motsvarighet för Britta Simon i moconavi som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt moconavi program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med moconavi:**

1. I Azure-portalen på den **moconavi** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. På den **moconavi domän och URL: er** avsnittet, utför följande steg:

    ![moconavi domän URL: er och enkel inloggning information](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2`

    C. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [moconavi klienten supportteamet](mailto:support@recomot.co.jp) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/moconavi-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **moconavi** sida, måste du skicka den hämtade **XML-Metadata för** till [moconavi supportteamet](mailto:support@recomot.co.jp). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/moconavi-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/moconavi-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/moconavi-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/moconavi-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-moconavi-test-user"></a>Skapa en testanvändare moconavi

I det här avsnittet skapar du en användare som kallas Britta Simon i moconavi. Arbeta med [moconavi supportteamet](mailto:support@recomot.co.jp) att lägga till användare i moconavi-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till moconavi.

![Tilldela rollen][200]

**Om du vill tilldela moconavi Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **moconavi**.

    ![Länken moconavi i listan med program](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

1. Installera moconavi från Microsoft store.

2. Starta moconavi.

3. Klicka på **ansluta inställningen** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing1.png)

4. Ange `https://mcs-admin.moconavi.biz/gateway` till **Anslut till URL: en** textrutan och klicka sedan på **klar** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing2.png)

5. Utför följande steg på följande skärmbild:

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing3.png)

    a. Ange **indata autentiseringsnyckel**:`azureAD` till **indata autentiseringsnyckel** textruta.

    b. Ange **indata användar-ID**: `your ad account` till **indata användar-ID** textruta.

    c. Klicka på **inloggning**.

6. Azure AD-lösenord för att ange **lösenord** textrutan och klicka sedan på **inloggning** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing4.png)

7. Azure AD-autentiseringen har lyckats när menyn visas.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

