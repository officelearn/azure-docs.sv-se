---
title: 'Självstudier: Azure Active Directory-integration med GetThere | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431316"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Självstudier: Azure Active Directory-integration med GetThere

I den här självstudien får du lära dig hur du integrerar GetThere med Azure Active Directory (AD Azure).

Integrera GetThere med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till GetThere.
- Du kan aktivera användarna att automatiskt få loggat in på GetThere (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med GetThere, behöver du följande objekt:

- En Azure AD-prenumeration
- En GetThere enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till GetThere från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-getthere-from-the-gallery"></a>Att lägga till GetThere från galleriet
För att konfigurera integrering av GetThere i Azure AD, som du behöver lägga till GetThere från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GetThere från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/getthere-tutorial/a_new_app.png)

4. I sökrutan skriver **GetThere**väljer **GetThere** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med GetThere baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i GetThere är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i GetThere upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med GetThere, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare GetThere](#create-a-getthere-test-user)**  – du har en motsvarighet för Britta Simon i GetThere som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt GetThere program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GetThere:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **GetThere** application integration markerar **enkel inloggning**.

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. I den **identifierare** text skriver du någon av de nedan URL: er:
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. I den **svars-URL** text skriver du någon av de nedan URL: er:
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. GetThere program som förväntar få det unika **användarnamn** värdet i anspråket användarnamn. Kunden kan mappa det korrekta värdet för användarnamn anspråket. I exemplet nedan som vi har mappat den **användarnamn** till **user.mail**, men du kan ändra mappningen enligt Organisationsinställningarna för din. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Namn |  Attribut för datakälla |  Namnrymd |
    | ---------------| --------------- | --------------- |
    | Webbplatsnamn | ”Ge värde enligt din organisations” | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Användarnamn |  User.Mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. I den **Namespace** textrutan skriver attributets namnområde visas för den raden.

    d. Välj källa som **attributet**.

    e. Från den **källattribut** anger attributvärdet som visas för den raden.

    f. Klicka på **Spara**.

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **certifikat (Base64)** och spara den på din dator.

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. På den **konfigurera GetThere** avsnittet, kopiera den lämpliga URL enligt dina behov.

    Observera att URL: en kan stå följande:

    a. Inloggningswebbadress

    b. Azure Ad-ID

    c. Utloggnings-URL

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. Att konfigurera enkel inloggning på **GetThere** sida, som du behöver skicka de hämtade **certifikat (Base64)** och kopierade **utloggnings-URL för inloggnings-URL, Azure Ad-ID,** till [ GetThere klienten supportteamet](mailto:dataintegration@sabre.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/getthere-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-getthere-test-user"></a>Skapa en GetThere testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i GetThere. Arbeta med [GetThere klienten supportteamet](mailto:dataintegration@sabre.com) att lägga till användare i GetThere-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till GetThere.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. I listan med program väljer **GetThere**.

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen GetThere i åtkomstpanelen du bör få automatiskt loggat in på ditt GetThere program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
