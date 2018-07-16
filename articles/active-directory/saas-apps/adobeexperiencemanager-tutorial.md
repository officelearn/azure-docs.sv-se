---
title: 'Självstudier: Azure Active Directory-integrering med Adobe Experience Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 56b392e57809cea0ae93800df39bb9dacd164ce2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054190"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Självstudier: Azure Active Directory-integrering med Adobe Experience Manager

I den här självstudien får du lära dig hur du integrerar Adobe Experience Manager med Azure Active Directory (AD Azure).

Integrera Adobe Experience Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe Experience Manager.
- Du kan aktivera användarna att få loggas in automatiskt till Adobe Experience Manager med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Adobe Experience Manager, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Adobe Experience Manager enkel inloggning aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö [skaffa en kostnadsfri utvärderingsversion för en månads](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Adobe Experience Manager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Lägg till Adobe Experience Manager från galleriet
För att konfigurera integrering av Adobe Experience Manager till Azure AD, som du behöver lägga till Adobe Experience Manager från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Adobe Experience Manager från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe Experience Manager**. Välj **Adobe Experience Manager** i resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Adobe Experience Manager i resultatlistan](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Adobe Experience Manager baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD att veta vilka motsvarande användare i Adobe Experience Manager är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i Adobe Experience Manager.

Adobe Experience Manager ger värdet **användarnamn** samma värde för den **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna. 

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Adobe Experience Manager:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) har en motsvarighet för Britta Simon i Adobe Experience Manager som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Adobe Experience Manager-program.

**Om du vill konfigurera Azure AD enkel inloggning med Adobe Experience Manager, gör du följande:**

1. I Azure-portalen på den **Adobe Experience Manager** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. Att aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** nedrullningsbara menyn och välj **SAML-baserad inloggning**.
 
    ![Enkel inloggning för dialogrutan](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. I den **Adobe Experience Manager-domän och URL: er** avsnittet, gör följande om du vill konfigurera appen i **IdP** läge:

    ![Adobe Experience Manager-domän och URL: er med enkel inloggning för information](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. I den **identifierare** skriver du ett unikt värde som du definierar serverns AEM. 

    b. I den **svars-URL** skriver du en URL med följande mönster: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med det faktiska ID: t och svars-URL. För att få dessa värden kan kontakta den [Adobe Experience Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html).
 
4. Kontrollera **visa avancerade URL-inställningar**. Sedan gör följande om du vill konfigurera programmet i **SP** initierade läge:

    ![Adobe Experience Manager-domän och URL: er med enkel inloggning för information](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    I den **inloggning på URL: en** skriver din Adobe Experience Manager server-URL. 

5. I den **SAML-signeringscertifikat** väljer **certifikat (Base64)**. Spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. För att öppna konfigurationsfönstret inloggning i konfigurationsavsnittet Adobe Experience Manager, Välj **Konfigurera Adobe Experience Manager**. Kopiera den **SAML inloggnings-tjänstens URL**, **SAML entitets-ID**, och **utloggning ID** från avsnittet Snabbreferens.

    ![Avsnittet konfigurationslänken](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Välj **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Öppna i ett nytt webbläsarfönster i **Adobe Experience Manager** administrationsportalen.

9. Välj **inställningar** > **Security** > **användare**.

    ![Konfigurera den enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Välj **administratör** eller andra relevanta användare.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Välj **kontoinställningar** > **hantera TrustStore**.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Under **Lägg till certifikat från CER-fil**, klickar du på **väljer certifikatfil**. Bläddra till och markera den certifikatfil som du redan har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certifikatet har lagts till TrustStore. Observera alias för certifikatet.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. På den **användare** väljer **autentiseringstjänst**.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Välj **kontoinställningar** > **skapa/hantera KeyStore**. Skapa KeyStore genom att ange ett lösenord.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Gå tillbaka till skärmen administratör. Välj sedan **inställningar** > **Operations** > **webbkonsolen**.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Detta öppnar konfigurationssidan.

    ![Konfigurera den enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Hitta **Adobe Granite SAML 2.0-autentisering hanteraren**. Välj sedan den **Lägg till** ikon.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Vidta följande åtgärder på den här sidan.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. I den **sökväg** anger **/**.

    b. I den **IDP URL** anger du den **SAML inloggnings-tjänstens URL** värde som du kopierade från Azure-portalen.

    c. I den **IDP certifikat Alias** anger du den **certifikat Alias** värde som du lade till i TrustStore.

    d. I den **Security tillhandahålls entitets-ID** anger det unika **SAML entitets-ID** värde som du konfigurerade i Azure-portalen.

    e. I den **URL för Konsumenttjänst för försäkran** anger du den **svars-URL** värde som du konfigurerade i Azure-portalen.

    f. I den **lösenord för nyckeln Store** anger du den **lösenord** som du angav i KeyStore.

    g. I den **användar-ID för attributet** anger du den **namn-ID** eller ett annat användar-ID som är relevanta i ditt fall.

    h. Välj **skapa CRX användare**.

    i. I den **URL för utloggning** anger det unika **URL: en för utloggning** värde som du fick från Azure-portalen.

    j. Välj **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken. Komma åt den inbäddade dokumentationen genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation på [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan överst i den **alla användare** dialogrutan **Lägg till**.

    ![Knappen Lägg till](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan användare](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** markerar du kryssrutan. Antecknar du värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Skapa en testanvändare Adobe Experience Manager

I det här avsnittet skapar du en användare som kallas Britta Simon i Adobe Experience Manager. Om du har valt den **skapa CRX användare** alternativet användare skapas automatiskt efter en lyckad autentisering. 

Om du vill skapa användare manuellt kan arbeta med den [Adobe Experience Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html) att lägga till användare i Adobe Experience Manager-plattformen. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till Adobe Experience Manager.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Adobe Experience Manager, gör du följande:**

1. Öppna program i Azure-portalen. Gå sedan till vyn directory väljer **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Adobe Experience Manager**.

    ![Adobe Experience Manager länken i listan med program](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Adobe Experience Manager i åtkomstpanelen, bör du få loggas in automatiskt till ditt Adobe Experience Manager-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

