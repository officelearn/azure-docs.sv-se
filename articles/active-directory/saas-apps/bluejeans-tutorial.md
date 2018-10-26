---
title: 'Självstudier: Azure Active Directory-integration med BlueJeans | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095236"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Självstudier: Azure Active Directory-integration med BlueJeans

I den här självstudien får du lära dig hur du integrerar BlueJeans med Azure Active Directory (AD Azure).

Integrera BlueJeans med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BlueJeans.
- Du kan aktivera användarna att automatiskt få loggat in på BlueJeans (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med BlueJeans, behöver du följande objekt:

- En Azure AD-prenumeration
- En BlueJeans enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till BlueJeans från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bluejeans-from-the-gallery"></a>Att lägga till BlueJeans från galleriet

För att konfigurera integrering av BlueJeans i Azure AD, som du behöver lägga till BlueJeans från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BlueJeans från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **BlueJeans**väljer **BlueJeans** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![BlueJeans i resultatlistan](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med BlueJeans baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i BlueJeans är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i BlueJeans upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med BlueJeans, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare BlueJeans](#creating-a-bluejeans-test-user)**  – du har en motsvarighet för Britta Simon i BlueJeans som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BlueJeans program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BlueJeans:**

1. I Azure-portalen på den **BlueJeans** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_general_301.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![BlueJeans domän och URL: er med enkel inloggning för information](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Värdet för inloggning är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [BlueJeans klienten supportteamet](https://support.bluejeans.com/contact) att hämta värdet.

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. På den **konfigurera BlueJeans** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Azure AD-ID

    c. Utloggnings-URL

    ![BlueJeans konfiguration](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. Logga in på i ett annat webbläsarfönster din **BlueJeans** företagets plats som administratör.

9. Gå till **ADMIN \> GRUPPINSTÄLLNINGAR \> SECURITY**.

    ![Administratören](./media/bluejeans-tutorial/IC785868.png "Admin")

10. I den **SECURITY** avsnittet, utför följande steg:

    ![SAML enkel inloggning](./media/bluejeans-tutorial/IC785869.png "SAML enkel inloggning")

    a. Välj **SAML enkel inloggning**.

    b. Välj **aktivera automatisk etablering**.

11. Gå vidare med följande steg:

    ![Certifikat sökväg](./media/bluejeans-tutorial/IC785870.png "certifikat sökväg")

    a. Klicka på **Välj fil**, för att ladda upp det Base64-kodade certifikatet som du har hämtat från Azure-portalen.

    b. I den **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I den **lösenordet ändra URL** textrutan klistra in värdet för **ändra lösenord URL** som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

12. Gå vidare med följande steg:

    ![Spara ändringar](./media/bluejeans-tutorial/IC785874.png "spara ändringar")

    a. I den **användar-id** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. I den **e-post** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klicka på **spara ändringar**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-bluejeans-test-user"></a>Skapa en BlueJeans testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i BlueJeans. BlueJeans stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](bluejeans-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på din **BlueJeans** företagets plats som administratör.

2. Gå till **ADMIN \> hantera användare \> Lägg till användare**.

    ![Administratören](./media/bluejeans-tutorial/IC785877.png "Admin")

    >[!IMPORTANT]
    >Den **Lägg till användare** fliken är endast tillgänglig om i den **SECUTIRY fliken**, **aktivera automatisk etablering** är avmarkerad. 

3. I den **Lägg till användare** avsnittet, utför följande steg:

    ![Lägg till användare](./media/bluejeans-tutorial/IC785886.png "lägga till användare")

    a. I **Förnamn** text, ange först namnet på användaren som **Britta**.

    b. I **efternamn** text anger efternamn för användaren som **simon**.

    c. I **välja ett användarnamn för BlueJeans** text, anger användarnamnet för användaren som **Brittasimon**

    d. I **skapa lösenord** text, ange ditt lösenord.

    e. I **företagets** text, ange ditt företag.

    f. I **e-postadress** text, ange den e-postadressen för användaren som **brittasimon@contoso.com**.

    g. I **skapa en BlueJeans möte I.D** text, ange ditt möte-ID.

    h. I **välja ett lösenord för Moderator** text, ange ditt lösenord.

    i. Klicka på **Fortsätt**.

    ![Addd användare](./media/bluejeans-tutorial/IC785887.png "Addd användare")

    J. Klicka på **Lägg till användare**.

>[!NOTE]
>Du kan använda alla andra BlueJeans användare konto verktyg för att skapa eller API: er som tillhandahålls av BlueJeans att etablera användarkonton i Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BlueJeans.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **BlueJeans**.

    ![Konfigurera enkel inloggning](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen BlueJeans i åtkomstpanelen du bör få automatiskt loggat in på ditt BlueJeans program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
