---
title: 'Självstudier: Azure Active Directory-integration med OnTrack | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OnTrack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: bf0fdb75a3091380557e1fbb2d69ed59aeb130bf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051391"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Självstudier: Azure Active Directory-integration med OnTrack

I den här självstudien får du lära dig hur du integrerar OnTrack med Azure Active Directory (AD Azure).

Integrera OnTrack med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OnTrack.
- Du kan aktivera användarna att automatiskt få loggat in på OnTrack (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OnTrack, behöver du följande objekt:

- En Azure AD-prenumeration
- En OnTrack enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till OnTrack från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ontrack-from-the-gallery"></a>Att lägga till OnTrack från galleriet
För att konfigurera integrering av OnTrack i Azure AD, som du behöver lägga till OnTrack från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OnTrack från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **OnTrack**väljer **OnTrack** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![OnTrack i resultatlistan](./media/ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OnTrack baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i OnTrack är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i OnTrack upprättas.

I OnTrack, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med OnTrack, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare OnTrack](#create-an-ontrack-test-user)**  – du har en motsvarighet för Britta Simon i OnTrack som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt OnTrack-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med OnTrack:**

1. I Azure-portalen på den **OnTrack** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/ontrack-tutorial/tutorial_ontrack_samlbase.png)

3. På den **OnTrack domän och URL: er** avsnittet, utför följande steg:

    ![OnTrack domän och URL: er med enkel inloggning för information](./media/ontrack-tutorial/tutorial_ontrack_url.png)

    a. I den **identifierare** textrutan
    
    För testmiljön, skriver du URL: en: `https://staging.insigniagroup.com/sso`

    Ange URL: en för produktionsmiljö: `https://oeaccessories.com/sso`

    b. I den **svars-URL** textrutan
    
    För testmiljön, skriver du URL: en: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Ange URL: en för produktionsmiljö: `https://igaccessories.com/sso/autonation.aspx`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/ontrack-tutorial/tutorial_ontrack_certificate.png)

5. OnTrack programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. 

    ![Konfigurera enkel inloggning](./media/ontrack-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | -------------- | ----------------|    
    | Användarrollen      | ”42F432” |
    | Hyperion-kod  | "12345" |

    > [!NOTE]
    > **Användarrollen** och **Hyperion kod** attribut mappas med Autonation användarroll och återförsäljare kod respektive. Dessa värden är bara exempel, Använd rätt kod för din integrering. Du kan kontakta [Autonation support](mailto:CustomerService@insigniagroup.com) för dessa värden.
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ontrack-tutorial/tutorial_attribute_04.png) 

    ![Konfigurera enkel inloggning](./media/ontrack-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/ontrack-tutorial/tutorial_general_400.png)

8. Att konfigurera enkel inloggning på **OnTrack** sida, som du behöver skicka de hämtade **XML-Metadata för** till [OnTrack supportteamet](mailto:CustomerService@insigniagroup.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ontrack-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ontrack-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ontrack-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ontrack-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-ontrack-test-user"></a>Skapa en OnTrack testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i OnTrack. Arbeta med [OnTrack supportteamet](mailto:CustomerService@insigniagroup.com) att lägga till användare i OnTrack-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OnTrack.

![Tilldela rollen][200] 

**Om du vill tilldela OnTrack Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **OnTrack**.

    ![Länken OnTrack i listan med program](./media/ontrack-tutorial/tutorial_ontrack_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen OnTrack i åtkomstpanelen du bör få automatiskt loggat in på ditt OnTrack-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ontrack-tutorial/tutorial_general_01.png
[2]: ./media/ontrack-tutorial/tutorial_general_02.png
[3]: ./media/ontrack-tutorial/tutorial_general_03.png
[4]: ./media/ontrack-tutorial/tutorial_general_04.png

[100]: ./media/ontrack-tutorial/tutorial_general_100.png

[200]: ./media/ontrack-tutorial/tutorial_general_200.png
[201]: ./media/ontrack-tutorial/tutorial_general_201.png
[202]: ./media/ontrack-tutorial/tutorial_general_202.png
[203]: ./media/ontrack-tutorial/tutorial_general_203.png

