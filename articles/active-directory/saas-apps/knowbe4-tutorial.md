---
title: 'Självstudier: Azure Active Directory-integration med KnowBe4 medvetenhet säkerhetsutbildning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och utbildning för KnowBe4 säkerhet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: c27af4e7bc88f24b76310336859740d8795f7cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449281"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Självstudier: Azure Active Directory-integration med KnowBe4 säkerhetsutbildning medvetenhet

Lär dig hur du integrerar KnowBe4 Security utbildning med Azure Active Directory (AD Azure) i den här självstudien.

Integrera KnowBe4 Security utbildning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till utbildning för KnowBe4 säkerhet.
- Du kan aktivera användarna att automatiskt få loggat in på KnowBe4 medvetenhet säkerhetsutbildning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med KnowBe4 säkerhetsutbildning medvetenhet, behöver du följande objekt:

- En Azure AD-prenumeration
- En KnowBe4 medvetenhet säkerhetsutbildning enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till KnowBe4 medvetenhet säkerhetsutbildning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Att lägga till KnowBe4 medvetenhet säkerhetsutbildning från galleriet
För att konfigurera integrering av KnowBe4 utbildning för säkerhet i Azure AD, som du behöver lägga till KnowBe4 medvetenhet säkerhetsutbildning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till KnowBe4 medvetenhet säkerhetsutbildning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **KnowBe4 medvetenhet säkerhetsutbildning**väljer **KnowBe4 medvetenhet säkerhetsutbildning** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![KnowBe4 medvetenhet säkerhetsutbildning i resultatlistan](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med KnowBe4 medvetenhet säkerhetsutbildning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i KnowBe4 medvetenhet säkerhetsutbildning är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i KnowBe4 medvetenhet säkerhetsutbildning upprättas.

I KnowBe4 för medvetenhet säkerhetsutbildning tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med utbildning för KnowBe4 säkerhet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare KnowBe4 medvetenhet säkerhetsutbildning](#create-a-knowbe4-security-awareness-training-test-user)**  – du har en motsvarighet för Britta Simon i KnowBe4 medvetenhet säkerhetsutbildning som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för KnowBe4 medvetenhet säkerhetsutbildning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med KnowBe4 medvetenhet säkerhetsutbildning:**

1. I Azure-portalen på den **KnowBe4 medvetenhet säkerhetsutbildning** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

1. På den **KnowBe4 säkerhetsdomän medvetenhet utbildning och URL: er** avsnittet, utför följande steg:

    ![KnowBe4 säkerhetsdomän medvetenhet utbildning och URL: er med enkel inloggning för information](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [KnowBe4 Säkerhetsklient medvetenhet utbildning supportteamet](mailto:support@KnowBe4.com) att hämta det här värdet. 

    b. I den **identifierare** textrutan skriver strängvärdet: `KnowBe4`

    > [!NOTE]
    >Detta är skiftlägeskänsligt

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/knowbe4-tutorial/tutorial_general_400.png)

1. På den **KnowBe4 säkerhetskonfiguration medvetenhet utbildning** klickar du på **konfigurera KnowBe4 medvetenhet säkerhetsutbildning** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![KnowBe4 säkerhetskonfiguration medvetenhet utbildning](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

1. Att konfigurera enkel inloggning på **KnowBe4 medvetenhet säkerhetsutbildning** sida, som du behöver skicka de hämtade **certifikat (Raw)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning Tjänst-URL för** till [KnowBe4 Säkerhetsklient medvetenhet utbildning supportteamet](mailto:support@KnowBe4.com).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/knowbe4-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/knowbe4-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/knowbe4-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Skapa en testanvändare KnowBe4 säkerhetsutbildning medvetenhet

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i KnowBe4 Security-utbildning. KnowBe4 medvetenhet säkerhetsutbildning stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt KnowBe4 säkerhetsutbildning medvetenhet om det inte finns ännu. 

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [KnowBe4 medvetenhet säkerhetsutbildning supportteamet](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till utbildning för KnowBe4 säkerhet.

![Tilldela rollen][200] 

**Om du vill tilldela KnowBe4 medvetenhet säkerhetsutbildning Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **KnowBe4 medvetenhet säkerhetsutbildning**.

    ![Länken KnowBe4 medvetenhet säkerhetsutbildning i listan med program](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
  
När du klickar på panelen KnowBe4 medvetenhet säkerhetsutbildning i åtkomstpanelen du bör få automatiskt loggat in på programmets KnowBe4 Security-utbildning. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png

