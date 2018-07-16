---
title: 'Självstudier: Azure Active Directory-integrering med ArcGIS Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: de41838b95b524780ca9df56ff036a200fd5f73a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043711"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Självstudier: Azure Active Directory-integrering med ArcGIS Online

I den här självstudien får du lära dig hur du integrerar ArcGIS Online med Azure Active Directory (AD Azure).

Integrera ArcGIS Online med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ArcGIS Online.
- Du kan aktivera användarna att automatiskt få loggat in på ArcGIS Online (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ArcGIS Online, behöver du följande objekt:

- En Azure AD-prenumeration
- En ArcGIS Online enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ArcGIS Online från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arcgis-online-from-the-gallery"></a>Att lägga till ArcGIS Online från galleriet
För att konfigurera integrering av ArcGIS Online i Azure AD, som du behöver lägga till ArcGIS Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till ArcGIS Online från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ArcGIS Online**väljer **ArcGIS Online** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ArcGIS Online i resultatlistan](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ArcGIS Online baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i ArcGIS Online är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ArcGIS Online upprättas.

I ArcGIS Online, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ArcGIS Online, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en ArcGIS Online testanvändare](#create-a-arcgis-online-test-user)**  – du har en motsvarighet för Britta Simon i ArcGIS Online som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmets ArcGIS Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ArcGIS Online:**

1. I Azure-portalen på den **ArcGIS Online** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. På den **ArcGIS Online domän och URL: er** avsnittet, utför följande steg:

    ![ArcGIS Online domän och URL: er med enkel inloggning för information](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.maps.arcgis.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [ArcGIS Online klienten supportteamet](http://support.esri.com/en/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/arcgis-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen för ArcGIS-företag som en administratör i ett annat webbläsarfönster.

7. Klicka på **redigera inställningar**.

    ![Redigera inställningar för](./media/arcgis-tutorial/ic784742.png "redigera inställningar")

8. Klicka på **Security**.

    ![Security](./media/arcgis-tutorial/ic784743.png "säkerhet")

9. Under **Enterprise inloggningar**, klickar du på **ange IDENTITETSPROVIDER**.

    ![Enterprise-inloggningar](./media/arcgis-tutorial/ic784744.png "Enterprise inloggningar")

10. På den **ange identitetsprovider** configuration utför följande steg:
   
    ![Ange identitetsprovider](./media/arcgis-tutorial/ic784745.png "ange identitetsprovider")
   
    a. I den **namn** textrutan skriver du namnet på din organisation.

    b. För **Metadata för identitetsprovidern Enterprise tillhandahålls med**väljer **A filen**.

    c. Om du vill ladda upp din hämtade metadatafilen, klickar du på **Välj fil**.

    d. Klicka på **SET IDENTITETSPROVIDER**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/arcgis-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/arcgis-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/arcgis-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/arcgis-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-arcgis-online-test-user"></a>Skapa en ArcGIS Online testanvändare

För att aktivera Azure AD-användare att logga in på ArcGIS Online, måste de etableras i ArcGIS Online.  
När det gäller ArcGIS Online är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **ArcGIS** klient.

2. Klicka på **inbjudan MEDLEMMAR**.
   
    ![Bjud in medlemmar](./media/arcgis-tutorial/ic784747.png "bjuda in medlemmar")

3. Välj **lägga till medlemmar automatiskt utan att skicka ett e-postmeddelande**, och klicka sedan på **nästa**.
   
    ![Lägg till medlemmar automatiskt](./media/arcgis-tutorial/ic784748.png "automatiskt lägga till medlemmar")

4. På den **medlemmar** dialogrutan utför följande steg:
   
     ![Lägga till och granska](./media/arcgis-tutorial/ic784749.png "Lägg till och granskning")
    
     a. Ange den **e-post**, **Förnamn**, och **efternamn** för ett giltigt AAD-konto som du vill etablera.
  
     b. Klicka på **Lägg till och granska**.
5. Granska de data du har angett och klicka sedan på **Lägg till MEDLEMMAR**.
   
    ![Lägg till medlem](./media/arcgis-tutorial/ic784750.png "Lägg till medlem")
        
    > [!NOTE]
    > Azure Active Directory-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till ArcGIS Online.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ArcGIS Online, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **ArcGIS Online**.

    ![ArcGIS Online länken i listan med program](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ArcGIS Online i åtkomstpanelen du bör få automatiskt loggat in på programmets ArcGIS Online.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

