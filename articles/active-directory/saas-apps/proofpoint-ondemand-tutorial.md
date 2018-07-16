---
title: 'Självstudier: Azure Active Directory-integration med Proofpoint på begäran | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proofpoint på begäran.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 36668708cc787e7d515efdaa1c2038b967a74093
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051735"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Självstudier: Azure Active Directory-integration med Proofpoint på begäran

I den här självstudien får du lära dig hur du integrerar Proofpoint på begäran med Azure Active Directory (AD Azure).

Integrera Proofpoint på begäran med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Proofpoint på begäran.
- Du kan aktivera användarna att automatiskt få loggat in på Proofpoint på begäran (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Proofpoint på begäran, behöver du följande objekt:

- En Azure AD-prenumeration
- En Proofpoint på begäran enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Proofpoint på begäran från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Att lägga till Proofpoint på begäran från galleriet
Om du vill konfigurera integreringen av Proofpoint på begäran till Azure AD, som du behöver lägga till Proofpoint på begäran från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Proofpoint på begäran från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Proofpoint på begäran**väljer **Proofpoint på begäran** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Proofpoint på begäran i resultatlistan](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Proofpoint på begäran baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Proofpoint på begäran till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Proofpoint på begäran upprättas.

I Proofpoint på begäran, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Proofpoint på begäran, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Proofpoint på begäran testanvändare](#create-a-proofpoint-on-demand-test-user)**  – du har en motsvarighet för Britta Simon i Proofpoint på begäran som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Proofpoint på begäran-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Proofpoint på begäran:**

1. I Azure-portalen på den **Proofpoint på begäran** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_samlbase.png)

3. På den **Proofpoint på begäran-domän och URL: er** avsnittet, utför följande steg:

    ![Proofpoint på begäran domän och URL: er enkel inloggning](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<hostname>.pphosted.com/ppssamlsp`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Proofpoint på begäran klienten supportteamet](https://www.proofpoint.com/us/support-services) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/proofpoint-ondemand-tutorial/tutorial_general_400.png)
    
7. På den **Proofpoint på begäran konfiguration** klickar du på **konfigurera Proofpoint på begäran** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Proofpoint på begäran-konfiguration](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_configure.png) 

8. Att konfigurera enkel inloggning på **Proofpoint på begäran** sida, som du behöver skicka de hämtade **Certificate(Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Proofpoint på begäran-supportteamet](https://www.proofpoint.com/us/support-services). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/proofpoint-ondemand-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/proofpoint-ondemand-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/proofpoint-ondemand-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-proofpoint-on-demand-test-user"></a>Skapa en Proofpoint på begäran testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Proofpoint på begäran. Arbeta med [Proofpoint på begäran klienten supportteamet](https://www.proofpoint.com/us/support-services) att lägga till användare i Proofpoint på begäran-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Proofpoint på begäran.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Proofpoint på begäran, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Proofpoint på begäran**.

    ![Proofpoint på begäran länk i listan med program](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Proofpoint på begäran-panel i åtkomstpanelen du bör få automatiskt loggat in på ditt Proofpoint på begäran-programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/proofpoint-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/proofpoint-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/proofpoint-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/proofpoint-ondemand-tutorial/tutorial_general_203.png

