---
title: 'Självstudier: Azure Active Directory-integrering med Organisationsschema nu | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Organisationsschema nu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f044b6fcefc0c0345ea6c83212da31c7c03c11f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202903"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Självstudier: Azure Active Directory-integrering med Organisationsschema nu

I den här självstudien får du lära dig hur du integrerar Organisationsschema nu med Azure Active Directory (AD Azure).

Integrera Organisationsschema nu med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till organisationsschema nu.
- Du kan aktivera användarna att automatiskt få loggat in på organisationsschema nu (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Organisationsschema nu behöver du följande objekt:

- En Azure AD-prenumeration
- En Organisationsschema nu enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till organisationsschema nu från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-orgchart-now-from-the-gallery"></a>Att lägga till organisationsschema nu från galleriet
För att konfigurera integrering av organisationsschema nu i Azure AD, som du behöver lägga till organisationsschema nu från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till organisationsschema nu från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Organisationsschema nu**väljer **Organisationsschema nu** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Organisationsschema nu i resultatlistan](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Organisationsschema nu till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren Organisationsschema nu upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Organisationsschema nu](#create-an-orgchart-now-test-user)**  – du har en motsvarighet för Britta Simon Organisationsschema nu som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för organisationsschema nu.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Organisationsschema nu:**

1. I Azure-portalen på den **Organisationsschema nu** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. På den **Organisationsschema nu domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Organisationsschema nu domän och URL: er med enkel inloggning för information](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    I den **identifierare** textrutan anger du ett URL: `https://sso2.orgchartnow.com`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Organisationsschema nu domän och URL: er med enkel inloggning för information](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` SAML entitets-ID som kopieras från avsnittet Snabbreferens beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. På den **Organisationsschema nu Configuration** klickar du på **konfigurera Organisationsschema nu** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet** och använda den för att slutföra **inloggnings-URL** i **Organisationsschema nu domän och URL: er avsnittet**.

    ![Organisationsschema nu konfiguration](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Att konfigurera enkel inloggning på **Organisationsschema nu** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Organisationsschema nu supportteamet](mailto:ocnsupport@officeworksoftware.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-orgchart-now-test-user"></a>Skapa en testanvändare Organisationsschema nu

Om du vill aktivera Azure AD-användare att logga in på organisationsschema nu etableras de i Organisationsschema nu. 

1. Organisationsschema nu stöder just-in-time-etablering, vilket är som standard aktiverat. En ny användare har skapats under ett försök att komma åt Organisationsschema nu om det inte finns ännu. Just-in-time-funktionen för användaretablering kommer bara att skapa en **skrivskyddad** användare när en SSO-förfrågan kommer från en känd IDP: N och e-post i SAML-försäkran kan inte hittas i användarlistan. För den här Automatisk etablering funktionen måste du skapa en åtkomstgrupp benämnt **Allmänt** Organisationsschema nu. Följ de stegen nedan för att skapa en åtkomstgrupp:

    a. Gå till den **hantera grupper** alternativet när du klickar på den **kugghjulsikonen** i det övre högra hörnet av Användargränssnittet.

    ![Organisationsschema nu grupper](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Välj den **Lägg till** ikon och namn på gruppen **Allmänt** klickar **OK**. 

    ![Organisationsschema nu lägga till](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Välj mapparna du vill Allmänt eller skrivskyddad användare ska kunna komma åt:

    ![Organisationsschema nu mappar](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Lås** mappar så att endast administratörer kan ändra dem. Tryck på **OK**.

    ![Låser Organisationsschema nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Skapa **Admin** användare och **Läs/Skriv** användare, måste du manuellt skapa en användare för att få åtkomst till sina Privilegienivå via enkel inloggning. Gör följande för att etablera ett användarkonto:

    a. Logga in på organisationsschema nu som en administratör.

    b.  Klicka på **inställningar** i övre högra hörnet och gå sedan till **hantera användare**.

    ![Organisationsschema nu inställningar](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicka på **Lägg till** och utför följande steg:

    ![Organisationsschema nu hantera](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * I den **användar-ID** textrutan Ange användar-ID som **brittasimon@contoso.com**.

    * I textrutan **E-postadress** anger du användarens e-postadress, som **brittasimon@contoso.com**.

    * Klicka på **Lägg till**.
    
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till organisationsschema nu.

![Tilldela rollen][200] 

**Om du vill tilldela Organisationsschema nu Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Organisationsschema nu**.

    ![Organisationsschema nu länken i listan med program](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Organisationsschema nu i åtkomstpanelen du bör få automatiskt loggat in på programmets Organisationsschema nu.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

