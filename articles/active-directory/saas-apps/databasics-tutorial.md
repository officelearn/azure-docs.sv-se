---
title: 'Självstudier: Azure Active Directory-integrering med DATABASICS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DATABASICS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: a37ded45-84c8-4e88-8d9b-c5b9443eb0d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: jeedes
ms.openlocfilehash: 06c79d331272251481572851b9dfbd8ecb99cea2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190045"
---
# <a name="tutorial-azure-active-directory-integration-with-databasics"></a>Självstudier: Azure Active Directory-integrering med DATABASICS

I den här självstudien får du lära dig hur du integrerar DATABASICS med Azure Active Directory (AD Azure).

Integrera DATABASICS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till DATABASICS.
- Du kan aktivera användarna att automatiskt få loggat in på DATABASICS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med DATABASICS, behöver du följande objekt:

- En Azure AD-prenumeration
- En DATABASICS enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till DATABASICS från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-databasics-from-the-gallery"></a>Att lägga till DATABASICS från galleriet
För att konfigurera integrering av DATABASICS i Azure AD, som du behöver lägga till DATABASICS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till DATABASICS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **DATABASICS**väljer **DATABASICS** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![DATABASICS i resultatlistan](./media/databasics-tutorial/tutorial_databasics_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med DATABASICS baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i DATABASICS är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i DATABASICS upprättas.

I DATABASICS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med DATABASICS, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare DATABASICS](#create-a-databasics-test-user)**  – du har en motsvarighet för Britta Simon i DATABASICS som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt DATABASICS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med DATABASICS:**

1. I Azure-portalen på den **DATABASICS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/databasics-tutorial/tutorial_databasics_samlbase.png)

1. På den **DATABASICS domän och URL: er** avsnittet, utför följande steg:

    ![DATABASICS domän och URL: er med enkel inloggning för information](./media/databasics-tutorial/tutorial_databasics_url.png)

    a. I den **identifierare** textrutan angett värde: `DATA-BASICS_SP`
    
    b. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<sitenumber>.data-basics.net/<clientname>/saml_sso.jsp`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [DATABASICS klienten supportteamet](https://www.data-basics.com/support/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/databasics-tutorial/tutorial_databasics_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/databasics-tutorial/tutorial_general_400.png)

1. Om du vill konfigurera enkel inloggning på DATABASICS sida, fyller du i formuläret med hjälp av Webbadressen som nedan. När formuläret skickas den [DATABASICS klienten supportteamet](https://www.data-basics.com/support/) kommer att kontakta dig.
    
    [https://www.data-basics.com/support/submit-sso-onboarding-request/](https://www.data-basics.com/support/submit-sso-onboarding-request/)


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/databasics-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/databasics-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/databasics-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/databasics-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-databasics-test-user"></a>Skapa en DATABASICS testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i DATABASICS. Arbeta med [DATABASICS supportteam](https://www.data-basics.com/support/) att lägga till användare i DATABASICS-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till DATABASICS.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon DATABASICS, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **DATABASICS**.

    ![Länken DATABASICS i listan med program](./media/databasics-tutorial/tutorial_databasics_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DATABASICS i åtkomstpanelen du bör få automatiskt loggat in på ditt DATABASICS program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/databasics-tutorial/tutorial_general_01.png
[2]: ./media/databasics-tutorial/tutorial_general_02.png
[3]: ./media/databasics-tutorial/tutorial_general_03.png
[4]: ./media/databasics-tutorial/tutorial_general_04.png

[100]: ./media/databasics-tutorial/tutorial_general_100.png

[200]: ./media/databasics-tutorial/tutorial_general_200.png
[201]: ./media/databasics-tutorial/tutorial_general_201.png
[202]: ./media/databasics-tutorial/tutorial_general_202.png
[203]: ./media/databasics-tutorial/tutorial_general_203.png

