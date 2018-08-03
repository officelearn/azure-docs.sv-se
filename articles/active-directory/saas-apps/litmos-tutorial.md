---
title: 'Självstudier: Azure Active Directory-integration med Litmos | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a0c70ee6419280b0975d77fb213f9406286708cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428010"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Självstudier: Azure Active Directory-integration med Litmos

I den här självstudien får du lära dig hur du integrerar Litmos med Azure Active Directory (AD Azure).

Integrera Litmos med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Litmos.
- Du kan aktivera användarna att automatiskt få loggat in på Litmos (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Litmos, behöver du följande objekt:

- En Azure AD-prenumeration
- En Litmos enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Litmos från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-litmos-from-the-gallery"></a>Att lägga till Litmos från galleriet
För att konfigurera integrering av Litmos i Azure AD, som du behöver lägga till Litmos från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Litmos från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Litmos**väljer **Litmos** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Litmos i resultatlistan](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Litmos baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Litmos är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Litmos upprättas.

I Litmos, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Litmos, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Litmos](#create-a-litmos-test-user)**  – du har en motsvarighet för Britta Simon i Litmos som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Litmos program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Litmos:**

1. I Azure-portalen på den **Litmos** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

1. På den **Litmos domän och URL: er** avsnittet, utför följande steg:

    ![Litmos domän och URL: er med enkel inloggning för information](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.litmos.com/account/Login`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och svars-URL som beskrivs senare i självstudien eller kontakta [Litmos supportteam](https://www.litmos.com/contact-us/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/litmos-tutorial/tutorial_litmos_certificate.png)

1. Som en del av konfigurationen, måste du anpassa den **SAML-Tokenattribut** för Litmos programmet.

    ![Attributet avsnittet](./media/litmos-tutorial/tutorial_attribute.png)
           
    | Attributnamn   | Attributvärde |   
    | ---------------  | ----------------|
    | FirstName |User.givenName |
    | LastName  |User.surname |
    | E-post |User.Mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Lägg till attribut](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![Lägg till attribut Dailog](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.     

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/litmos-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster inloggning till webbplatsen Litmos företag som administratör.

1. I navigeringsfältet till vänster, klickar du på **konton**.
   
    ![Kontoavsnittet på App-sida][22] 

1. Klicka på den **integreringar** fliken.
   
    ![Fliken Integration][23] 

1. På den **integreringar** fliken, rulla ned till **3 part integreringar**, och klicka sedan på **SAML 2.0** fliken.
   
    ![SAML 2.0 avsnittet][24] 

1. Kopiera värdet under **The SAML-slutpunkten för litmos är:** och klistra in den i den **svars-URL** -textrutan i den **Litmos domän och URL: er** avsnitt i Azure-portalen. 
   
    ![SAML-slutpunkt][26] 

1. I din **Litmos** program, utför följande steg:
    
     ![Litmos program][25] 
     
     a. Klicka på **aktivera SAML**.
    
     b. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **SAML X.509-certifikat** textrutan.
     
     c. Klicka på **spara ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/litmos-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/litmos-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/litmos-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/litmos-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-litmos-test-user"></a>Skapa en Litmos testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Litmos.  
Litmos programmet stöder Just-in-Time-etablering. Det innebär att ett användarkonto skapas automatiskt om det behövs vid ett försök till åtkomst till programmet från åtkomstpanelen.

**Om du vill skapa en användare som kallas Britta Simon i Litmos, utför du följande steg:**

1. I ett annat webbläsarfönster inloggning till webbplatsen Litmos företag som administratör.

1. I navigeringsfältet till vänster, klickar du på **konton**.
   
    ![Kontoavsnittet på App-sida][22] 

1. Klicka på den **integreringar** fliken.
   
    ![Fliken integreringar][23] 

1. På den **integreringar** fliken, rulla ned till **3 part integreringar**, och klicka sedan på **SAML 2.0** fliken.
   
    ![SAML 2.0][24] 
    
1. Välj **skapa användare**
   
    ![Skapa användare][27] 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Litmos.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Litmos, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Litmos**.

    ![Länken Litmos i listan med program](./media/litmos-tutorial/tutorial_litmos_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  

När du klickar på panelen Litmos i åtkomstpanelen du bör få automatiskt loggat in på ditt Litmos program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png

