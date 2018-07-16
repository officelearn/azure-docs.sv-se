---
title: 'Självstudier: Azure Active Directory-integration med MobileIron | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: jeedes
ms.openlocfilehash: 25bb4ba9cfb15657cf28f7018ad622c9dfee9458
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051939"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Självstudier: Azure Active Directory-integration med MobileIron

I den här självstudien får du lära dig hur du integrerar MobileIron med Azure Active Directory (AD Azure).

Integrera MobileIron med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till MobileIron.
- Du kan aktivera användarna att automatiskt få loggat in på MobileIron (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MobileIron, behöver du följande objekt:

- En Azure AD-prenumeration
- En MobileIron enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till MobileIron från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mobileiron-from-the-gallery"></a>Att lägga till MobileIron från galleriet
För att konfigurera integrering av MobileIron i Azure AD, som du behöver lägga till MobileIron från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MobileIron från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **MobileIron**väljer **MobileIron** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![MobileIron i resultatlistan](./media/mobileiron-tutorial/tutorial_mobileiron_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med MobileIron baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i MobileIron är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i MobileIron upprättas.

I MobileIron, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med MobileIron, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare MobileIron](#create-a-mobileiron-test-user)**  – du har en motsvarighet för Britta Simon i MobileIron som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt MobileIron program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med MobileIron:**

1. I Azure-portalen på den **MobileIron** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/mobileiron-tutorial/tutorial_mobileiron_samlbase.png)

3. På den **MobileIron domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![MobileIron domän och URL: er med enkel inloggning för information](./media/mobileiron-tutorial/tutorial_mobileiron_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.mobileiron.com/<key>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![MobileIron domän och URL: er som enkel inloggning](./media/mobileiron-tutorial/tutorial_mobileiron_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<host>.mobileiron.com/user/login.html`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Du får du värdet för nyckeln och värden från den administrativa MobileIron som beskrivs senare i självstudien-portalen.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/mobileiron-tutorial/tutorial_mobileiron_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mobileiron-tutorial/tutorial_general_400.png)

7. I ett annat webbläsarfönster logga du in på webbplatsen MobileIron företag som administratör.

8. Gå till **Admin** > **identitet**.

   * Välj **AAD** alternativet i den **information om molnet IDP installationsprogrammet** fält.

    ![Konfigurera enkel inloggning för Admin-knapp](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

9. Kopiera värdena för **nyckel** och **värden** och klistra in dem för att slutföra URL: er i den **MobileIron domän och URL: er** avsnitt i Azure-portalen.

    ![Konfigurera enkel inloggning för Admin-knapp](./media/mobileiron-tutorial/key.png)

10. I den **Export metadatafilen från AAD och importera MobileIron molnet fältet** klickar du på **Välj fil** att ladda upp den hämtade metadatan från Azure-portalen. Klicka på **klar** överförda.
 
    ![Konfigurera enkel inloggning för admin metadata knapp](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mobileiron-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mobileiron-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mobileiron-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mobileiron-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-mobileiron-test-user"></a>Skapa en MobileIron testanvändare

Om du vill aktivera Azure AD-användare att logga in på MobileIron, måste de etableras i MobileIron.  
När det gäller MobileIron är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen MobileIron företag som administratör.

2. Gå till **användare** och klicka på **lägga till** > **enkel användaren**.

    ![Konfigurera enkel inloggning för användare-knappen](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

3. På den **”enanvändarläge”** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning för användaren Lägg till knapp](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. I **e-postadress** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Förnamn** text, ange först namnet på användaren som Britta.

    c. I **efternamn** text anger efternamn för användaren som Simon.
    
    d. Klicka på **Klar**.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MobileIron.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon MobileIron, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **MobileIron**.

    ![Länken MobileIron i listan med program](./media/mobileiron-tutorial/tutorial_mobileiron_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen MobileIron i åtkomstpanelen du bör få automatiskt loggat in på ditt MobileIron program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/mobileiron-tutorial/tutorial_general_01.png
[2]: ./media/mobileiron-tutorial/tutorial_general_02.png
[3]: ./media/mobileiron-tutorial/tutorial_general_03.png
[4]: ./media/mobileiron-tutorial/tutorial_general_04.png

[100]: ./media/mobileiron-tutorial/tutorial_general_100.png

[200]: ./media/mobileiron-tutorial/tutorial_general_200.png
[201]: ./media/mobileiron-tutorial/tutorial_general_201.png
[202]: ./media/mobileiron-tutorial/tutorial_general_202.png
[203]: ./media/mobileiron-tutorial/tutorial_general_203.png

