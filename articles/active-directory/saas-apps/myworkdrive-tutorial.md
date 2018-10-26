---
title: 'Självstudier: Azure Active Directory-integration med MyWorkDrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094064"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Självstudier: Azure Active Directory-integration med MyWorkDrive

I den här självstudien får du lära dig hur du integrerar MyWorkDrive med Azure Active Directory (AD Azure).

Integrera MyWorkDrive med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till MyWorkDrive.
- Du kan aktivera användarna att automatiskt få loggat in på MyWorkDrive (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MyWorkDrive, behöver du följande objekt:

- En Azure AD-prenumeration
- En MyWorkDrive enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till MyWorkDrive från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-myworkdrive-from-the-gallery"></a>Att lägga till MyWorkDrive från galleriet
För att konfigurera integrering av MyWorkDrive i Azure AD, som du behöver lägga till MyWorkDrive från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MyWorkDrive från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/myworkdrive-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/myworkdrive-tutorial/a_new_app.png)

4. I sökrutan skriver **MyWorkDrive**väljer **MyWorkDrive** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med MyWorkDrive baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i MyWorkDrive är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i MyWorkDrive upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med MyWorkDrive, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare MyWorkDrive](#create-a-myworkdrive-test-user)**  – du har en motsvarighet för Britta Simon i MyWorkDrive som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt MyWorkDrive program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med MyWorkDrive:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **MyWorkDrive** application integration markerar **enkel inloggning**.

    ![image](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Klicka på **ange ytterligare webbadresser** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL.  Ange ditt företag MyWorkDrive Server värd name:e.g.
    > 
    > Svars-URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Inloggnings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakta supportteamet för MyWorkDrive klienten om du är osäker på hur du ställer in dina egna värdnamn och SSL-certifikat för dessa värden.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på Kopiera **ikonen** att kopiera **Appfederationsmetadata** och klicka på **hämta** att ladda ned den **certifikat (Base64)** spara den på din dator.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. På den **konfigurera MyWorkDrive** avsnittet, kopiera den lämpliga URL enligt dina behov.

    Observera att URL: en kan stå följande:

    a. Inloggningswebbadress

    b. Azure Ad-ID

    c. Utloggnings-URL

    ![image](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Om du vill konfigurera enkel inloggning på MyWorkDrive sida, ladda ned den **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML inloggnings-tjänst-URL för enkel** och konfigurera dem manuellt på MyWorkDrive server eller kopiera och klistra in Azure  **Appfederationsmetadata** till din MyWorkDrive Server Admin panelen SAML Azure AD-konfigurationsskärmen. För ytterligare information Kontakta [MyWorkDrive supportteamet](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/myworkdrive-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/myworkdrive-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-myworkdrive-test-user"></a>Skapa en testanvändare MyWorkDrive

I det här avsnittet skapar du en användare som kallas Britta Simon i MyWorkDrive. Arbeta med [MyWorkDrive supportteamet](mailto:support@myworkdrive.com) att lägga till användare i MyWorkDrive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MyWorkDrive.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/myworkdrive-tutorial/d_all_applications.png)

2. I listan med program väljer **MyWorkDrive**.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/myworkdrive-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen MyWorkDrive i åtkomstpanelen du bör få automatiskt loggat in på ditt MyWorkDrive program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
