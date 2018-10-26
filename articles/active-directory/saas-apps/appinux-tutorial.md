---
title: 'Självstudier: Azure Active Directory-integration med Appinux | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appinux.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f329341b-fb77-42e5-b6a6-0cd641d19670
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: a9b8e7738f2974b4c672af0e50e2ad510f0d41c2
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50098114"
---
# <a name="tutorial-azure-active-directory-integration-with-appinux"></a>Självstudier: Azure Active Directory-integration med Appinux

I den här självstudien får du lära dig hur du integrerar Appinux med Azure Active Directory (AD Azure).

Integrera Appinux med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Appinux.
- Du kan aktivera användarna att automatiskt få loggat in på Appinux (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Appinux, behöver du följande objekt:

- En Azure AD-prenumeration
- En Appinux enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Appinux från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-appinux-from-the-gallery"></a>Att lägga till Appinux från galleriet

För att konfigurera integrering av Appinux i Azure AD, som du behöver lägga till Appinux från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Appinux från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Appinux**väljer **Appinux** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Appinux i resultatlistan](./media/appinux-tutorial/tutorial_appinux_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Appinux baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Appinux är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Appinux upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Appinux, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Appinux](#creating-an-appinux-test-user)**  – du har en motsvarighet för Britta Simon i Appinux som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Appinux program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Appinux:**

1. I Azure-portalen på den **Appinux** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/appinux-tutorial/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/appinux-tutorial/tutorial_appinux_editurl.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![Appinux domän och URL: er med enkel inloggning för information](./media/appinux-tutorial/tutorial_appinux_url1.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<Appinux_SUBDOMAIN>.appinux.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<Appinux_SUBDOMAIN>.appinux.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Appinux klienten supportteamet](https://support.appinux.com) att hämta dessa värden.

5. Appinux program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![Attributet avsnittet](./media/appinux-tutorial/edit_attribute.png)

    ![Attributet avsnittet](./media/appinux-tutorial/tutorial_namespace.png)

6. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | **Namn** | **Namespace** | **Attribut för datakälla**|
    | ---------|---------------| --------- |
    | `givenname` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.givenname` |
    | `surname` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.surname` |
    | `emailaddress` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.mail` |
    | `name` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.userprincipalname` |
    | `UserType` | `http://bcv.appinux.com/claims` | `Provide the value as per your organization` |
    | `Tag` | `http://appinux.com/Tag` | `Provide the value as per your organization` |
    | `Role` | `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` | `user.assignedroles` |
    | `email` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/email` | `user.mail` |
    | `wanshort` | `http://appinux.com/windowsaccountname2` | `extractmailprefix([userprincipalname])` |
    | `nameidentifier` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.employeeid` |

    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![Nytt attribut](./media/appinux-tutorial/new_save_attribute.png)

    ![Lägg till attribut](./media/appinux-tutorial/new_attribute_details.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. I den **Namespace** textrutan skriver namnområdesvärdet som visas för den raden.

    d. Välj källa som **attributet**.

    e. Från den **källattribut** anger attributvärdet som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara för metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/appinux-tutorial/tutorial_appinux_certificate.png) 

8. Att konfigurera enkel inloggning på **Appinux** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Appinux supportteamet](https://support.appinux.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/appinux-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/appinux-tutorial/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-an-appinux-test-user"></a>Skapa en Appinux testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Appinux. Appinux stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Appinux om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Appinux supportteamet](https://support.appinux.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Appinux.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Appinux**.

    ![Konfigurera enkel inloggning](./media/appinux-tutorial/tutorial_appinux_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Appinux i åtkomstpanelen du bör få automatiskt loggat in på ditt Appinux program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appinux-tutorial/tutorial_general_01.png
[2]: ./media/appinux-tutorial/tutorial_general_02.png
[3]: ./media/appinux-tutorial/tutorial_general_03.png
[4]: ./media/appinux-tutorial/tutorial_general_04.png

[100]: ./media/appinux-tutorial/tutorial_general_100.png

[200]: ./media/appinux-tutorial/tutorial_general_200.png
[201]: ./media/appinux-tutorial/tutorial_general_201.png
[202]: ./media/appinux-tutorial/tutorial_general_202.png
[203]: ./media/appinux-tutorial/tutorial_general_203.png
