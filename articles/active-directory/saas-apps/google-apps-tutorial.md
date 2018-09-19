---
title: 'Självstudier: Azure Active Directory-integration med G Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: 9281c5fe04e3f29d1e3425edb4841dfaca1cd9e2
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294840"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Självstudier: Azure Active Directory-integration med G Suite

I den här självstudien får du lära dig hur du integrerar G Suite med Azure Active Directory (AD Azure).

Integrera G Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till G Suite.
- Du kan aktivera användarna att automatiskt få loggat in på G Suite (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med G Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En G Suite enkel inloggning aktiverat prenumeration
- En prenumeration för Google Apps eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
1.  **F: kan den här integreringsstöd Google Cloud Platform SSO-integrering med Azure AD?**
    
    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP integrationen måste du konfigurera SSO med Google Apps.


1. **F: är Chromebooks och andra Chrome enheter kompatibla med Azure AD enkel inloggning?**
   
    S: Ja, användare kan logga in på sina Chromebook-enheter med sina autentiseringsuppgifter för Azure AD. Se den här [G Suite support-artikeln](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan få du tillfrågas om autentiseringsuppgifter två gånger.

1. **F: om jag aktiverar enkel inloggning, kommer användare att kunna använda sina autentiseringsuppgifter för Azure AD för att logga in på en Google-produkt, till exempel Google Classroom, GMail, Google Drive, YouTube och så vidare?**
   
    S: Ja, beroende på [vilka G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

1. **F: kan jag aktivera enkel inloggning för endast en delmängd av Mina G Suite-användare?**
   
    S: Nej, aktivera enkel inloggning omedelbart kräver att alla dina G Suite-användare att autentisera med sina autentiseringsuppgifter för Azure AD. Eftersom G Suite har inte stöd för att ha flera Identitetsproviders, identitetsleverantören för din G Suite-miljö kan antingen vara Azure AD eller Google – men inte båda på samma gång.

1. **F: om en användare har loggat in via Windows, är de automatiskt autentisera till G Suite utan ett lösenord uppmanas?**
   
    S: det finns två alternativ för att aktivera det här scenariot. Först måste användare kan logga in på Windows 10-enheter via [Azure Active Directory Join](../device-management-introduction.md). Du kan också användare kan logga in på Windows-enheter som är anslutna till en domän till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) distribution. I båda fallen måste du utföra stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och G Suite.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till G Suite från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-g-suite-from-the-gallery"></a>Att lägga till G Suite från galleriet
Om du vill konfigurera integreringen av G Suite till Azure AD, som du behöver lägga till G Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till G Suite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **G Suite**väljer **G Suite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![G Suite i resultatlistan](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med G Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i G Suite är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i G Suite upprättas.

I G Suite, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med G Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för G Suite](#create-a-g-suite-test-user)**  – du har en motsvarighet för Britta Simon i G Suite som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt G Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med G Suite:**

1. I Azure-portalen på den **G Suite** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

1. På den **G Suite-domän och URL: er** om du vill konfigurera för den **Gmail** utför följande steg:

    ![G Suite-domän och URL: er med enkel inloggning för information](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [G Suite klienten supportteamet](https://www.google.com/contact/) att hämta dessa värden.

1. På den **G Suite-domän och URL: er** om du vill konfigurera för den **Google Cloud Platform** utför följande steg:

    ![G Suite-domän och URL: er med enkel inloggning för information](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [G Suite klienten supportteamet](https://www.google.com/contact/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/google-apps-tutorial/tutorial_general_400.png)

1. På den **G Suite-konfiguration** klickar du på **konfigurera G Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML enkel inloggning för tjänst-URL och ändra URL: en lösenord** från den **Snabbreferens avsnittet.**

    ![G Suite-konfiguration](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

1. Öppna en ny flik i webbläsaren och logga in på den [G Suite-administratörskonsolen](http://admin.google.com/) med ditt administratörskonto.

1. Klicka på **Security**. Om du inte ser länken, det kan vara dolda den **fler kontroller** menyn längst ned på skärmen.
   
    ![Klicka på Security.][10]

1. På den **Security** klickar du på **Konfigurera enkel inloggning (SSO).**
   
    ![Klicka på enkel inloggning.][11]

1. Utför följande konfigurationsändringar:
   
    ![Konfigurera enkel inloggning][12]
   
    a. Välj **installationsprogrammet SSO med tredjeparts identitetsprovider**.

    b. I den **inloggning sid-URL** fältet i G Suite, klistra in värdet för **enkel inloggnings-URL för** som du har kopierat från Azure-portalen.

    c. I den **utloggning sid-URL** fältet i G Suite, klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen. 

    d. I den **ändra lösenord URL: en** fältet i G Suite, klistra in värdet för **ändra lösenord URL: en** som du har kopierat från Azure-portalen. 

    e. I G Suite för den **verifieringscertifikatet**, ladda upp det certifikat som du har hämtat från Azure-portalen.

    f. Välj **använder en specifik domän-provider**.

    g. Klicka på **spara ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/google-apps-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/google-apps-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/google-apps-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/google-apps-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-g-suite-test-user"></a>Skapa en testanvändare för G Suite

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i G Suite-program. G Suite har stöd för automatisk etablering, vilket är som standard aktiverat. Det finns inga åtgärder åt dig i det här avsnittet. Om en användare inte redan finns i G Suite-program, skapas en ny när du försöker få åtkomst till G Suite-program.

>[!NOTE] 
>Om du vill skapa en användare manuellt kan du kontakta den [Google support-teamet](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till G Suite.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon G Suite, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **G Suite**.

    ![G Suite-länk i listan med program](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen G Suite i åtkomstpanelen du bör få automatiskt loggat in på ditt G Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

