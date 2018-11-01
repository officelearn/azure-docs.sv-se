---
title: 'Självstudier: Azure Active Directory-integrering med 8 x 8 virtuella Office | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 8 x 8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741818"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Självstudier: Azure Active Directory-integrering med 8 x 8 virtuella Office

I den här självstudien får du lära dig hur du integrerar 8 x 8 virtuella Office med Azure Active Directory (AD Azure).

Integrera 8 x 8 ger virtuella Office med Azure AD dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till 8 x 8 virtuella Office.
- Du kan aktivera användarna att automatiskt få loggat in på 8 x 8 virtuella Office (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med 8 x 8 virtuella Office, behöver du följande objekt:

- En Azure AD-prenumeration
- En 8 x 8 virtuella Office enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till 8 x 8 virtuella Office från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Att lägga till 8 x 8 virtuella Office från galleriet

Om du vill konfigurera integreringen av 8 x 8 virtuella Office till Azure AD, som du behöver lägga till 8 x 8 virtuella Office från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 8 x 8 virtuella Office från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **8 x 8 virtuella Office**väljer **8 x 8 virtuella Office** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![8 x 8 virtuella Office i resultatlistan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med 8 x 8 virtuella Office baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i 8 x 8 virtuella Office är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i 8 x 8 virtuella Office måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med 8 x 8 virtuella Office, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för 8 x 8 virtuella Office](#creating-a-8x8-virtual-office-test-user)**  – du har en motsvarighet för Britta Simon i 8 x 8 virtuella Office som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt 8 x 8 virtuella Office-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med 8 x 8 virtuella Office:**

1. I Azure-portalen på den **8 x 8 virtuella Office** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![8 x 8 virtuella Office-domän och URL: er enkel inloggning för information](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://sso.8x8.com/saml2`

    b. I den **svars-URL** textrutan anger du ett URL: `https://sso.8x8.com/saml2`

5. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. På den **installerade 8 x 8 virtuella Office** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![8 x 8 virtuella Office-konfiguration](common/configuresection.png)

7. Inloggning till 8 x 8 virtuella Office-klienten som administratör.

8. Välj **virtuella Office konto hanteraren** på panelen för programmet.

    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Välj **företag** konto för att hantera och klicka på **logga In** knappen.

    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Klicka på **konton** fliken i listan över menyn.

    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Klicka på **Single Sign On** i listan över konton.
  
    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Välj **Single Sign On** under autentiseringsmetoder och klicka på **SAML**.

    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. I den **SAML enkelinloggning** avsnittet, utför följande steg:

    ![Konfigurera på App-sida](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. I den **logga i URL: en** textrutan klistra in **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    b. I den **Utloggning** textrutan klistra in **URL för utloggning** värde som du har kopierat från Azure-portalen.

    c. I den **utfärdar-URL** textrutan klistra in **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.

    d. Klicka på **Bläddra** knappen för att ladda upp det certifikat som du har hämtat från Azure-portalen.

    e. Klicka på knappen **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Skapa en 8 x 8 virtuella Office testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i 8 x 8 virtuella Office. 8 x 8 virtuella Office stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att få åtkomst till 8 x 8 virtuella Office om det inte finns ännu.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta den [8 x 8 virtuella Office-supportteamet](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 8 x 8 virtuella Office.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **8 x 8 virtuella Office**.

    ![Konfigurera enkel inloggning](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på 8 x 8 virtuella Office-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt 8 x 8 virtuella Office-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
