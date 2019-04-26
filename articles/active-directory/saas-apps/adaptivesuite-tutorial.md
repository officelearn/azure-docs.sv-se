---
title: 'Självstudier: Azure Active Directory-integrering med anpassningsbar insikter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och anpassningsbar insikter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285638"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Självstudier: Azure Active Directory-integrering med anpassningsbar insikter

Lär dig hur du integrerar anpassningsbar insikter med Azure Active Directory (AD Azure) i den här självstudien.

Integrera anpassningsbar insikter med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till anpassningsbara insikter.
- Du kan aktivera användarna att automatiskt få loggat in på anpassningsbar insikter (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med anpassningsbar insikter, behöver du följande objekt:

- En Azure AD-prenumeration
- En anpassningsbar insikter enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till anpassad Insights från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adaptive-insights-from-the-gallery"></a>Lägga till anpassad Insights från galleriet
Om du vill konfigurera integreringen av anpassningsbar insikter om Azure AD, som du behöver lägga till anpassningsbar insikter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till anpassad insikter från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. I sökrutan skriver **anpassningsbar Insights**väljer **anpassningsbar Insights** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med anpassningsbar insikter utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i anpassningsbar insikter är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i anpassningsbar Insights upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med anpassningsbar Insights, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en anpassningsbar insikter testanvändare](#create-an-adaptive-insights-test-user)**  – du har en motsvarighet för Britta Simon i anpassningsbar insikter som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för anpassningsbar insikter.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med anpassningsbar Insights:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **anpassningsbar Insights** application integration markerar **enkel inloggning**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** intiated läge:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Du kan hämta identifierare (entitets-ID) och svars-URL-värden från de anpassningsbara insikter **SAML SSO-inställningar** sidan.
 
5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **Certifikat (Base64)** och spara det på datorn.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. På den **ställa in anpassningsbar insikter** avsnittet, kopiera den lämpliga URL enligt dina behov.

    Observera att URL: en kan stå följande:

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. I ett annat webbläsarfönster logga du in på webbplatsen för företagets anpassningsbar insikter som administratör.

8. Gå till **Admin**.

    ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

9. I den **användare och roller** klickar du på **hantera inställningar för SAML SSO**.

    ![Hantera inställningar för enkel inloggning SAML](./media/adaptivesuite-tutorial/IC805645.png "hantera SAML SSO-inställningar")

10. På den **SAML SSO-inställningar** utför följande steg:

    ![Inställningar för enkel inloggning SAML](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO-inställningar")

    a. I den **namn på identitetsprovider** textrutan anger du ett namn för din konfiguration.

    b. Klistra in den **Azure Ad-identifierare** värdet som har kopierats från Azure-portalen till den **identitetsprovider entitets-ID** textrutan.

    c. Klistra in den **inloggnings-URL** värdet som har kopierats från Azure-portalen till den **identitetsprovider SSO URL** textrutan.

    d. Klistra in den **URL för utloggning** värdet som har kopierats från Azure-portalen till den **anpassad URL för utloggning** textrutan.

    e. Om du vill ladda upp din hämtade certifikatet klickar du på **Välj fil**.

    f. Välj följande, för:

    * **Användar-id för SAML**väljer **användarens anpassningsbar information användarnamn**.

    * **SAML-id Användarplats**väljer **användar-id i NameID ämne**.

    * **SAML NameID-format**väljer **e-postadress**.

    * **Aktivera SAML**väljer **Tillåt SAML SSO och anpassningsbar Insights direktinloggning**.

    g. Kopiera **anpassningsbar Insights SSO-URL** och klistra in i den **identifierare (entitets-ID)** och **svars-URL** textrutor i den **anpassningsbar insikter domän och URL: er** avsnitt i Azure-portalen.

    h. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Skapa en anpassningsbar insikter testanvändare

Om du vill aktivera Azure AD-användare att logga in på anpassningsbar insikter, måste de etableras till anpassningsbar insikter. När det gäller anpassningsbar insikter är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:** 

1. Logga in på din **anpassningsbar insikter** företagets plats som administratör.
2. Gå till **Admin**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. I den **användare och roller** klickar du på **Lägg till användare**.

   ![Lägg till användare](./media/adaptivesuite-tutorial/IC805648.png "Lägg till användare")
   
4. I den **ny användare** avsnittet, utför följande steg:

   ![Skicka](./media/adaptivesuite-tutorial/IC805649.png "skicka")

   a. Typen i **namn**, **inloggning**, **e-post**, **lösenord** av en giltig Azure Active Directory-användare du vill etablera i den relaterade textrutor.

   b. Välj en **rollen**.

   c. Klicka på **Skicka**.

>[!NOTE]
>Du kan använda anpassningsbar insikter användaren-konto skapas verktyg från någon annan eller API: er som tillhandahålls av anpassningsbar insikter att etablera AAD-användarkonton.
>

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till anpassningsbar insikter.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. I listan med program väljer **anpassningsbar insikter**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen anpassningsbar insikter i åtkomstpanelen du bör få automatiskt loggat in på programmets anpassningsbar insikter.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
