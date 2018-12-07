---
title: 'Självstudier: Azure Active Directory-integration med TextMagic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: 5ab193d908063230946ebb2bb6320ab50bf14971
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014896"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Självstudier: Azure Active Directory-integration med TextMagic

I den här självstudien får du lära dig hur du integrerar TextMagic med Azure Active Directory (AD Azure).

Integrera TextMagic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TextMagic.
- Du kan aktivera användarna att automatiskt få loggat in på TextMagic (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TextMagic, behöver du följande objekt:

- En Azure AD-prenumeration
- En TextMagic enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TextMagic från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-textmagic-from-the-gallery"></a>Att lägga till TextMagic från galleriet

För att konfigurera integrering av TextMagic i Azure AD, som du behöver lägga till TextMagic från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TextMagic från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **TextMagic**väljer **TextMagic** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![TextMagic i resultatlistan](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TextMagic baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TextMagic är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TextMagic upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med TextMagic, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TextMagic](#creating-a-textmagic-test-user)**  – du har en motsvarighet för Britta Simon i TextMagic som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TextMagic program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TextMagic:**

1. I Azure-portalen på den **TextMagic** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![TextMagic domän och URL: er med enkel inloggning för information](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    I den **identifierare** textrutan anger du ett URL: `https://my.textmagic.com/saml/metadata`

5. TextMagic program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut och anspråk** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Namn  | Attribut för datakälla  | Namnrymd |
    | --------------- | --------------- | --------------- |
    | Företagets | User.CompanyName | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Förnamn               | User.givenName |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Efternamn            | User.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefon               | User.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. Klicka på **redigeringsikonen** att redigera den **namnge identifierarvärde** från **user.userprinicipalname** till **user.mail**.

    ![TextMagic attribut](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. I den **namn**textrutan skriver du attributnamnet som visas för den raden.

    d. Ange den **Namespace** värde.

    e. Välj källa som **attributet**.

    f. Från den **källattribut** anger attributvärdet som visas för den raden.

    g. Klicka på **OK**.

    h. Klicka på **Spara**. 

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. På den **konfigurera TextMagic** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![TextMagic konfiguration](common/configuresection.png)

9. I ett annat webbläsarfönster logga du in på webbplatsen TextMagic företag som administratör.

10. Välj **kontoinställningar** under användarnamnet.

    ![TextMagic konfiguration](./media/textmagic-tutorial/config1.png)

11. Klicka på FLIKEN **enkel inloggning (SSO)** och Fyll i följande fält:  
    
    ![TextMagic konfiguration](./media/textmagic-tutorial/config2.png)

    a. I **identitetsprovider entitets-ID:** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    b. I **SSO-URL för identitetsprovider:** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. I **SLO-URL för identitetsprovider:** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    d. Öppna din **Base64-kodade certifikat** i anteckningar som hämtats från Azure-portalen, kopierar du innehållet i den till Urklipp och klistra in den till den **offentliga x509 certifikat:** textrutan.

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
  
### <a name="creating-a-textmagic-test-user"></a>Skapa en TextMagic testanvändare

Program stöder **Just-in-time användaretablering** och när autentiserade användare kommer att skapas i programmet automatiskt. Du måste fylla i informationen om en gång vid första inloggningen till aktivera underordnade konto i systemet.
Det finns inga uppgift åt dig i det här avsnittet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TextMagic.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **TextMagic**.

    ![Konfigurera enkel inloggning](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TextMagic i åtkomstpanelen du bör få automatiskt loggat in på ditt TextMagic program.
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
