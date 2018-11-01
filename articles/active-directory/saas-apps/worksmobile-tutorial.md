---
title: 'Självstudier: Azure Active Directory-integrering med rad WORKS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och rad fungerar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 0437bd46537d8b7024618706aba12660abca5512
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414700"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Självstudier: Azure Active Directory-integration med rad fungerar

I den här självstudien får du lära dig hur du integrerar rad fungerar med Azure Active Directory (AD Azure).

Integrera rad fungerar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till rad fungerar.
- Du kan aktivera användarna att automatiskt få loggat in på rad fungerar (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med rad fungerar, behöver du följande objekt:

- En Azure AD-prenumeration
- En rad fungerar enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till raden fungerar från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-line-works-from-the-gallery"></a>Att lägga till raden fungerar från galleriet
För att konfigurera integrering av rad fungerar i Azure AD, som du behöver lägga till raden fungerar från galleriet i din lista över hanterade SaaS-appar.

**Lägg till rad fungerar från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/worksmobile-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/worksmobile-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/worksmobile-tutorial/a_new_app.png)

4. I sökrutan skriver **rad fungerar**väljer **rad fungerar** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med rad fungerar baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i rad fungerar är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i rad fungerar upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med rad fungerar, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en rad fungerar testanvändare](#create-a-line-works-test-user)**  – du har en motsvarighet för Britta Simon i rad fungerar som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för rad fungerar.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med rad fungerar:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **rad fungerar** application integration markerar **enkel inloggning**.

    ![image](./media/worksmobile-tutorial/b1_b2_select_sso.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/worksmobile-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/worksmobile-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://auth.worksmobile.com/d/login/<domain>/?userId=<ID@domain>`

    b. I den **identifierare** text skriver en URL: `worksmobile.com`

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **certifikat (Raw)** och spara den på din dator.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_certficate.png)

6. På den **konfigurera rad fungerar** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![image](./media/worksmobile-tutorial/d1_samlsonfigure.png) 

7. Att konfigurera enkel inloggning på **rad fungerar** sida, Läs igenom den [rad fungerar SSO dokument](https://developers.worksmobile.com/jp/document/1001080101) och konfigurera en inställning för rad fungerar.

> [!NOTE]
> Du behöver att konvertera den nedladdade filen för certifikatet från .cert till PEM


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/worksmobile-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/worksmobile-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/worksmobile-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-line-works-test-user"></a>Skapa en rad fungerar testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i rad fungerar. Åtkomst [rad fungerar Administrationssida](https://admin.worksmobile.com) och lägga till användare i rad fungerar-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till rad fungerar.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/worksmobile-tutorial/d_all_applications.png)

2. I listan med program väljer **rad fungerar**.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/worksmobile-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/worksmobile-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen rad fungerar i åtkomstpanelen du bör få automatiskt loggat in på programmets rad fungerar.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


