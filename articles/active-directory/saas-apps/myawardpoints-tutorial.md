---
title: 'Självstudier: Azure Active Directory-integrering med min Award punkter upp Sub/Top-teamet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mina Award punkter upp Sub/Top-teamet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ed1c12768eed0265f65388984a5859b8f0630b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210738"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Självstudier: Azure Active Directory-integrering med min Award punkter upp Sub/Top-teamet

I den här självstudien får lära du att integrera Mina Award punkter upp Sub/Top-teamet med Azure Active Directory (AD Azure).

Integrera Mina Award punkter upp Sub/Top-teamet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till min Award punkter upp Sub/Top-teamet.
- Du kan aktivera användarna att automatiskt få loggat in på min Award punkter upp Sub/Top-teamet (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med min Award punkter upp Sub/Top-teamet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mina Award punkter upp Sub/Top-teamet enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till min Award punkter upp Sub/Top-teamet från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Att lägga till min Award punkter upp Sub/Top-teamet från galleriet

För att konfigurera integrering av Mina Award punkter upp Sub/Top-teamet i Azure AD, som du behöver lägga till min Award punkter upp Sub/Top-teamet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till min Award punkter upp Sub/Top-teamet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Mina Award punkter upp Sub/Top-teamet**väljer **Mina Award punkter upp Sub/Top-teamet** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Min Award punkter upp Sub/Top-teamet i resultatlistan](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i min Award punkter upp Sub/Top-teamet till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mina Award punkter upp Sub/Top-teamet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare i Mina Award punkter upp Sub/Top-teamet](#create-a-my-award-points-top-subtop-team-test-user)**  – du har en motsvarighet för Britta Simon i Mina Award punkter upp Sub/Top-teamet som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för min Award punkter upp Sub/Top-teamet.

**Om du vill konfigurera Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet, utför du följande steg:**

1. I Azure-portalen på den **Mina Award punkter upp Sub/Top-teamet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. På den **Mina Award punkter upp Sub/upp Team domän och URL: er** avsnittet, utför följande steg:

    ![Min Award punkter upp Sub/upp Team domän och URL: er med enkel inloggning för information](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Du får den `<SAMLENTITYID>` värdet i senare steg i den här självstudien.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. I den **min Award punkter upp Sub/upp Team konfiguration** väljer **Konfigurera mina Award punkter upp Sub/Top-teamet** att öppna fönstret Konfigurera inloggning. Kopiera SAML entitets-ID från den **Snabbreferens** avsnittet och lägger till SAML entitets-ID-värdet med URL: en i stället för inloggning `<SAMLENTITYID>` i den **Mina Award punkter upp Sub/upp Team domän och URL: er** avsnitt i Azure-portalen.

7. Att konfigurera enkel inloggning på **Mina Award punkter upp Sub/Top-teamet** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Mina Award punkter upp Sub/Top-teamet supportteamet](mailto:myawardpoints@biworldwide.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Skapa en testanvändare i Mina Award punkter upp Sub/Top-teamet

I det här avsnittet skapar du en användare som kallas Britta Simon i Mina Award punkter upp Sub/Top-teamet. Arbeta med [Mina Award punkter upp Sub/Top-teamet supportteamet](mailto:myawardpoints@biworldwide.com) att lägga till användare i min Award punkter upp Sub/Top-teamet-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till min Award punkter upp Sub/Top-teamet.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Mina Award punkter upp Sub/Top-teamet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Mina Award punkter upp Sub/Top-teamet**.

    ![Länken Mina Award punkter upp Sub/Top-teamet i listan med program](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mina Award punkter upp Sub/Top-teamet i åtkomstpanelen du bör få automatiskt loggat in på ditt program för min Award punkter upp Sub/Top-teamet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png
