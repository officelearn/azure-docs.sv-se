---
title: 'Självstudier: Azure Active Directory-integration med EverBridge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 75bd4325063ff90e4a065c0cf854fa375e58fa95
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584799"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudier: Azure Active Directory-integration med EverBridge

I den här självstudien får du lära dig hur du integrerar EverBridge med Azure Active Directory (AD Azure).

Integrera EverBridge med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till EverBridge.
- Du kan aktivera användarna att automatiskt få loggat in på EverBridge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EverBridge, behöver du följande objekt:

- En Azure AD-prenumeration
- En EverBridge enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till EverBridge från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-everbridge-from-the-gallery"></a>Att lägga till EverBridge från galleriet

För att konfigurera integrering av EverBridge i Azure AD, som du behöver lägga till EverBridge från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EverBridge från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **EverBridge**väljer **EverBridge** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![EverBridge i resultatlistan](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med EverBridge baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i EverBridge är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i EverBridge upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med EverBridge, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare EverBridge](#creating-an-everbridge-test-user)**  – du har en motsvarighet för Britta Simon i EverBridge som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt EverBridge program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med EverBridge:**

1. I Azure-portalen på den **EverBridge** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

    >[!NOTE]
    >Du behöver göra konfigurationer av programmet som Manager-portalen eller som medlem portalen i bägge ändar dvs. på Azure Portal och Everbridge-portalen.

4. Så här konfigurerar du den **EverBridge** programmet som **EverBridge Manager-portalen**på den **SAML grundkonfiguration** avsnittet utför följande steg:

    ![EverBridge domän och URL: er med enkel inloggning för information](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://sso.everbridge.net/<API_Name>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [EverBridge supportteamet](mailto:support@everbridge.com) att hämta dessa värden.

5. Så här konfigurerar du den **EverBridge** programmet som **EverBridge medlem Portal**på den **grundkonfiguration SAML** avsnittet, utför följande steg:

    * Om du vill konfigurera programmet i **IDP** initierade läge:

        ![EverBridge domän och URL: er med enkel inloggning för information](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * Klicka på **ange ytterligare webbadresser** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

        ![EverBridge domän och URL: er med enkel inloggning för information](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och URL: en inloggning. Kontakta [EverBridge supportteamet](mailto:support@everbridge.com) att hämta dessa värden.

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara för metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. På den **konfigurera EverBridge** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![EverBridge konfiguration](common/configuresection.png)

8. Att hämta SSO konfigurerats för **EverBridge** som **EverBridge Manager-portalen** program, utför följande steg: 
 
9. I ett annat webbläsarfönster, logga in på EverBridge som administratör.

9. Klicka på menyn längst upp i **inställningar** fliken och markera **enkel inloggning** under **Security**.
   
    ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. I den **namn** textrutan skriver du namnet på ID-providern (till exempel: namnet på ditt företag).
   
    b. I den **API-namn** textrutan skriver du namnet på API: et.
   
    c. Klicka på **Välj fil** knappen för att ladda upp metadatafilen som du har hämtat från Azure-portalen.
   
    d. I SAML identitet plats, väljer **identitet är i NameIdentifier-elementet i instruktionen ämne**.
   
    e. I den **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.
   
    f. I den Provider initierade begära Tjänstbindning, väljer **omdirigering för HTTP**.

    g. Klicka på **Spara**

10. Att konfigurera enkel inloggning på **EverBridge** programmet som **EverBridge medlem Portal**, du behöver skicka de hämtade **XML-Metadata för Federation** till [ Everbridge supportteamet](mailto:support@everbridge.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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
  
### <a name="creating-an-everbridge-test-user"></a>Skapa en EverBridge testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Everbridge. Arbeta med [EverBridge supportteamet](mailto:support@everbridge.com) att lägga till användare i Everbridge-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EverBridge.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **EverBridge**.

    ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen EverBridge i åtkomstpanelen du bör få automatiskt loggat in på ditt EverBridge program.
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
