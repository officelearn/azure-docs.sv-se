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
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 3284202ffaa6767a8dd4a6a5050dbdc928075237
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846130"
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

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ArcGIS Online från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arcgis-online-from-the-gallery"></a>Att lägga till ArcGIS Online från galleriet

För att konfigurera integrering av ArcGIS Online i Azure AD, som du behöver lägga till ArcGIS Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till ArcGIS Online från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. I sökrutan skriver **ArcGIS Online**väljer **ArcGIS Online** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ArcGIS Online baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i ArcGIS Online är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ArcGIS Online upprättas.

I ArcGIS Online, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ArcGIS Online, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en ArcGIS Online testanvändare](#create-an-arcgis-online-test-user)**  – du har en motsvarighet för Britta Simon i ArcGIS Online som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmets ArcGIS Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ArcGIS Online:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **ArcGIS Online** application integration markerar **enkel inloggning**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://<companyname>.maps.arcgis.com`.

    b. I den **identifierare** text skriver en URL med hjälp av följande mönster: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [ArcGIS Online klienten supportteamet](https://support.esri.com/en/) att hämta dessa värden.

6. På den **SAML-signeringscertifikat** avsnittet, klicka på **hämta** att ladda ned **Federation Metadata XML** och spara xml-filen på datorn.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Automatisera konfiguration i **ArcGIS Online**, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![image](./media/arcgis-tutorial/install_extension.png)

8. När du lägger till tillägg till webbläsaren, klickar på **konfigurera ArcGIS Online** omdirigerar dig till programmet ArcGIS Online. Ange administratörsautentiseringsuppgifter för att logga in på ArcGIS Online därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 9-13.

9. Om du vill konfigurera ArcGIS Online manuellt, öppna ett nytt webbläsarfönster och logga in på webbplatsen ArcGIS företag som administratör och utför följande steg:

10. Klicka på **redigera inställningar**.

    ![Redigera inställningar för](./media/arcgis-tutorial/ic784742.png "redigera inställningar")

11. Klicka på **Security**.

    ![Security](./media/arcgis-tutorial/ic784743.png "säkerhet")

12. Under **Enterprise inloggningar**, klickar du på **ange IDENTITETSPROVIDER**.

    ![Enterprise-inloggningar](./media/arcgis-tutorial/ic784744.png "Enterprise inloggningar")

13. På den **ange identitetsprovider** configuration utför följande steg:

    ![Ange identitetsprovider](./media/arcgis-tutorial/ic784745.png "ange identitetsprovider")

    a. I den **namn** textrutan skriver du namnet på din organisation.

    b. För **Metadata för identitetsprovidern Enterprise tillhandahålls med**väljer **A filen**.

    c. Om du vill ladda upp din hämtade metadatafilen, klickar du på **Välj fil**.

    d. Klicka på **SET IDENTITETSPROVIDER**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-an-arcgis-online-test-user"></a>Skapa en ArcGIS Online testanvändare

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

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. I listan med program väljer **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ArcGIS Online i åtkomstpanelen du bör få automatiskt loggat in på programmets ArcGIS Online.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



