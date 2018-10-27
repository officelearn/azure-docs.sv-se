---
title: 'Självstudier: Azure Active Directory-integration med BorrowBox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: jeedes
ms.openlocfilehash: a8ed2f04bf3004907cdd6e33bfb30260233fb101
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157164"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Självstudier: Azure Active Directory-integration med BorrowBox

I den här självstudien får du lära dig hur du integrerar BorrowBox med Azure Active Directory (AD Azure).

Integrera BorrowBox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BorrowBox.
- Du kan aktivera användarna att automatiskt få loggat in på BorrowBox (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med BorrowBox, behöver du följande objekt:

- En Azure AD-prenumeration
- En BorrowBox enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till BorrowBox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-borrowbox-from-the-gallery"></a>Att lägga till BorrowBox från galleriet
För att konfigurera integrering av BorrowBox i Azure AD, som du behöver lägga till BorrowBox från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BorrowBox från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./common/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./common/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./common/a_new_app.png)

4. I sökrutan skriver **BorrowBox**väljer **BorrowBox** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med BorrowBox baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i BorrowBox är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i BorrowBox upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med BorrowBox, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare BorrowBox](#create-a-borrowbox-test-user)**  – du har en motsvarighet för Britta Simon i BorrowBox som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BorrowBox program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BorrowBox:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **BorrowBox** application integration markerar **enkel inloggning**.

    ![image](./common/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./common/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./common/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. Klicka på **ange ytterligare webbadresser**.

    b. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [BorrowBox klienten supportteamet](mailto:borrowbox@bolinda.com) att hämta värdet.

5. BorrowBox program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut och anspråk** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/borrowbox-tutorial/i4-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    a. Klicka på **redigeringsikonen** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/borrowbox-tutorial/i2-attribute.png)

    ![image](./media/borrowbox-tutorial/i3-attribute.png)

    b. Från den **källattribut** väljer **user.mail**.

    c. Klicka på **Spara**. 

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned rätt certifikat som uppfyller dina krav och spara den på din dator.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

8. Att konfigurera enkel inloggning på **BorrowBox** sida, som du behöver skicka certifikat/metadata som du har hämtat från Azure portal för att [BorrowBox supportteamet](mailto:borrowbox@bolinda.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./common/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./common/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./common/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-borrowbox-test-user"></a>Skapa en BorrowBox testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i BorrowBox. BorrowBox stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt BorrowBox om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [BorrowBox supportteamet](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BorrowBox.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./common/d_all_applications.png)

2. I listan med program väljer **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./common/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./common/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen BorrowBox i åtkomstpanelen du bör få automatiskt loggat in på ditt BorrowBox program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
