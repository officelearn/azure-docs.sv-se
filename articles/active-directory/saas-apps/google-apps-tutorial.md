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
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 4ed571d34e5df67f556f39b898e7ae5efc06a3e1
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288942"
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

1. **F: kan den här integreringsstöd Google Cloud Platform SSO-integrering med Azure AD?**

    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP integrationen måste du konfigurera SSO med Google Apps.

2. **F: är Chromebooks och andra Chrome enheter kompatibla med Azure AD enkel inloggning?**
  
    S: Ja, användare kan logga in på sina Chromebook-enheter med sina autentiseringsuppgifter för Azure AD. Se den här [G Suite support-artikeln](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan få du tillfrågas om autentiseringsuppgifter två gånger.

3. **F: om jag aktiverar enkel inloggning, kommer användare att kunna använda sina autentiseringsuppgifter för Azure AD för att logga in på en Google-produkt, till exempel Google Classroom, GMail, Google Drive, YouTube och så vidare?**

    S: Ja, beroende på [vilka G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

4. **F: kan jag aktivera enkel inloggning för endast en delmängd av Mina G Suite-användare?**

    S: Nej, aktivera enkel inloggning omedelbart kräver att alla dina G Suite-användare att autentisera med sina autentiseringsuppgifter för Azure AD. Eftersom G Suite har inte stöd för att ha flera Identitetsproviders, identitetsleverantören för din G Suite-miljö kan antingen vara Azure AD eller Google – men inte båda på samma gång.

5. **F: om en användare har loggat in via Windows, är de automatiskt autentisera till G Suite utan ett lösenord uppmanas?**

    S: det finns två alternativ för att aktivera det här scenariot. Först måste användare kan logga in på Windows 10-enheter via [Azure Active Directory Join](../device-management-introduction.md). Du kan också användare kan logga in på Windows-enheter som är anslutna till en domän till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) distribution. I båda fallen måste du utföra stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och G Suite.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till G Suite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-g-suite-from-the-gallery"></a>Att lägga till G Suite från galleriet

Om du vill konfigurera integreringen av G Suite till Azure AD, som du behöver lägga till G Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till G Suite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/google-apps-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/google-apps-tutorial/a_select_app.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/google-apps-tutorial/a_new_app.png)

4. I sökrutan skriver **G Suite**väljer **G Suite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![image](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med G Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i G Suite är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i G Suite upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med G Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för G Suite](#create-a-g-suite-test-user)**  – du har en motsvarighet för Britta Simon i G Suite som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt G Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med G Suite:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **G Suite** application integration markerar **enkel inloggning**.

    ![image](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![image](./media/google-apps-tutorial/b1-domains_and_urls.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [G Suite klienten supportteamet](https://www.google.com/contact/) att hämta dessa värden.

6. G Suite-program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

7. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    a. Klicka på **redigera** knappen för att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Från den **källattribut** lista selelct attributvärdet.

    c. Klicka på **Spara**.

8. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned rätt certifikat som uppfyller dina krav och spara den på din dator.

    ![image](./media/google-apps-tutorial/certificatebase64.png)

9. På den **konfigurera G Suite** avsnittet, kopiera den lämpliga URL enligt dina behov.

    Observera att URL: en kan stå följande:

    a. Inloggningswebbadress

    b. Azure Ad-ID

    c. Utloggnings-URL

    ![image](./media/google-apps-tutorial/d1_saml.png) 

10. Öppna en ny flik i webbläsaren och logga in på den [G Suite-administratörskonsolen](http://admin.google.com/) med ditt administratörskonto.

11. Klicka på **Security**. Om du inte ser länken, det kan vara dolda den **fler kontroller** menyn längst ned på skärmen.

    ![Klicka på Security.][10]

12. På den **Security** klickar du på **Konfigurera enkel inloggning (SSO).**

    ![Klicka på enkel inloggning.][11]

13. Utför följande konfigurationsändringar:

    ![Konfigurera enkel inloggning][12]

    a. Välj **installationsprogrammet SSO med tredjeparts identitetsprovider**.

    b. I den **inloggning sid-URL** fältet i G Suite, klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I den **utloggning sid-URL** fältet i G Suite, klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    d. I den **ändra lösenord URL: en** fältet i G Suite, klistra in värdet för **ändra lösenord URL: en** som du har kopierat från Azure-portalen.

    e. I G Suite för den **verifieringscertifikatet**, ladda upp det certifikat som du har hämtat från Azure-portalen.

    f. Välj **använder en specifik domän-provider**.

    g. Klicka på **spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/google-apps-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/google-apps-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/google-apps-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-g-suite-test-user"></a>Skapa en testanvändare för G Suite

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i G Suite-program. G Suite har stöd för automatisk etablering, vilket är som standard aktiverat. Det finns inga åtgärder åt dig i det här avsnittet. Om en användare inte redan finns i G Suite-program, skapas en ny när du försöker få åtkomst till G Suite-program.

> [!NOTE]
> Kontrollera att användaren finns redan i G Suite om etablering i Azure AD inte har aktiverats innan du testar enkel inloggning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta den [Google support-teamet](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till G Suite.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/google-apps-tutorial/d_all_applications.png)

2. I listan med program väljer **G Suite**.

    ![image](./media/google-apps-tutorial/d_all_proapplications.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/google-apps-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/google-apps-tutorial/d_assign_user.png)

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen G Suite i åtkomstpanelen du bör få automatiskt loggat in på ditt G Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png