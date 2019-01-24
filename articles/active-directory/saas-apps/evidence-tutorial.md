---
title: 'Självstudier: Azure Active Directory-integrering med Evidence.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evidence.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: c1eebca0677cf14d59cf24e1a7acb5cebc692648
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812785"
---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Självstudier: Azure Active Directory-integrering med Evidence.com

I den här självstudien får du lära dig hur du integrerar Evidence.com med Azure Active Directory (AD Azure).

Integrera Evidence.com med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Evidence.com.
- Du kan aktivera användarna att automatiskt få loggat in på Evidence.com (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Evidence.com, behöver du följande objekt:

- En Azure AD-prenumeration
- En Evidence.com enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Evidence.com från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-evidencecom-from-the-gallery"></a>Att lägga till Evidence.com från galleriet
För att konfigurera integrering av Evidence.com i Azure AD, som du behöver lägga till Evidence.com från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Evidence.com från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Evidence.com**väljer **Evidence.com** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Evidence.com i resultatlistan](./media/evidence-tutorial/tutorial_evidence.com_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Evidence.com baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Evidence.com är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Evidence.com upprättas.

I Evidence.com, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Evidence.com, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Evidence.com](#create-a-evidencecom-test-user)**  – du har en motsvarighet för Britta Simon i Evidence.com som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Evidence.com program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Evidence.com:**

1. I Azure-portalen på den **Evidence.com** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/evidence-tutorial/tutorial_evidence.com_samlbase.png)

1. På den **Evidence.com domän och URL: er** avsnittet, utför följande steg:

    ![Evidence.com domän och URL: er med enkel inloggning för information](./media/evidence-tutorial/tutorial_evidence.com_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<yourtenant>.evidence.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<yourtenant>.evidence.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Evidence.com klienten supportteamet](https://communities.taser.com/support/SupportContactUs?typ=LE) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/evidence-tutorial/tutorial_evidence.com_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/evidence-tutorial/tutorial_general_400.png)

1. På den **Evidence.com Configuration** klickar du på **konfigurera Evidence.com** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Evidence.com konfiguration](./media/evidence-tutorial/tutorial_evidence.com_configure.png) 

1. I ett separat webbläsarfönster, logga in på din Evidence.com klient som administratör och navigera till **Admin** fliken

1. Klicka på **myndighet för enkel inloggning**

1. Välj **SAML-baserad enkel inloggning på**

1. Kopiera den **SAML entitets-ID**, **SAML enkel inloggning för tjänst-URL** och **URL: en för utloggning** värden som visas i Azure-portalen och att motsvarande fält i Evidence.com.

1. Öppna din hämtade Certificate(Base64)-filen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **säkerhetscertifikat** box. 

1. Spara konfigurationen i Evidence.com.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/evidence-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/evidence-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/evidence-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/evidence-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-evidencecom-test-user"></a>Skapa en Evidence.com testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras för åtkomst i Evidence.com-programmet. Det här avsnittet beskriver hur du skapar Azure AD-användarkonton i Evidence.com

**Att etablera ett användarkonto i Evidence.com:**

1. Logga in på webbplatsen Evidence.com företag som en administratör i ett webbläsarfönster.

1. Gå till **Admin** fliken.

1. Klicka på **lägga till användare**.

1. Klicka på knappen **Lägg till**.

1. Den **e-postadress** för den tillagda användaren måste matcha användarnamnet för användare i Azure AD som du vill ge åtkomst. Om användarnamnet och e-postadress inte är samma värde i din organisation, kan du använda den **Evidence.com > attribut > enkel inloggning** avsnitt av Azure portal för att ändra nameidenitifer som skickas till Evidence.com ska vara den e-postadress.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Evidence.com.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Evidence.com, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Evidence.com**.

    ![Länken Evidence.com i listan med program](./media/evidence-tutorial/tutorial_evidence.com_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Evidence.com i åtkomstpanelen du bör få automatiskt loggat in på ditt Evidence.com program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/evidence-tutorial/tutorial_general_01.png
[2]: ./media/evidence-tutorial/tutorial_general_02.png
[3]: ./media/evidence-tutorial/tutorial_general_03.png
[4]: ./media/evidence-tutorial/tutorial_general_04.png

[100]: ./media/evidence-tutorial/tutorial_general_100.png

[200]: ./media/evidence-tutorial/tutorial_general_200.png
[201]: ./media/evidence-tutorial/tutorial_general_201.png
[202]: ./media/evidence-tutorial/tutorial_general_202.png
[203]: ./media/evidence-tutorial/tutorial_general_203.png

