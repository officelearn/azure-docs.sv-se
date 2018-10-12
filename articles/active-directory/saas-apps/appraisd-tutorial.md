---
title: 'Självstudier: Azure Active Directory-integration med Appraisd | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118667"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Självstudier: Azure Active Directory-integration med Appraisd

I den här självstudien får du lära dig hur du integrerar Appraisd med Azure Active Directory (AD Azure).

Integrera Appraisd med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Appraisd.
- Du kan aktivera användarna att automatiskt få loggat in på Appraisd (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Appraisd, behöver du följande objekt:

- En Azure AD-prenumeration
- En Appraisd enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Appraisd från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-appraisd-from-the-gallery"></a>Att lägga till Appraisd från galleriet
För att konfigurera integrering av Appraisd i Azure AD, som du behöver lägga till Appraisd från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Appraisd från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. I sökrutan skriver **Appraisd**väljer **Appraisd** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Appraisd baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Appraisd är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Appraisd upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Appraisd, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Appraisd](#create-an-appraisd-test-user)**  – du har en motsvarighet för Britta Simon i Appraisd som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Appraisd program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Appraisd:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Appraisd** application integration markerar **enkel inloggning**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Klicka på **ange ytterligare webbadresser**. 

    b. I den **Vidarebefordransstatus** text skriver en URL: `<TENANTCODE>`

    c. Om du vill konfigurera programmet i **SP** har initierat läge, den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Du får det faktiska värdet för inloggnings-URL och Vidarebefordransstatus på sidan Appraisd SSO-konfiguration som beskrivs senare i självstudien.
    
5. Appraisd program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Klicka på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    a. Klicka på **redigera** knappen för att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Från den **källattribut** väljer attributvärdet.

    c. Klicka på **Spara**. 

7. I den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **certifikat (Base64)** och spara den på din dator.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. På den **konfigurera Appraisd** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Azure Ad-ID

    c. Utloggnings-URL

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. I ett annat webbläsarfönster, loggar du in Appraisd som en administratör.

10. Längst upp till höger på sidan, klickar du på **inställningar** ikonen och gå sedan till **Configuration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. Från vänster sida av menyn, klickar du på **SAML enkel inloggning**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. På den **SAML 2.0 enkel inloggning configuration** utför följande steg:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiera den **standard Vidarebefordransstatus** och klistra in den i **Vidarebefordransstatus** -textrutan i **grundläggande SAML-konfiguration** på Azure-portalen.

    b. Kopiera den **tjänstinitierade inloggnings-URL** och klistra in den i **inloggnings-URL** -textrutan i **SAML grundkonfiguration** på Azure-portalen.

13. Rulla nedåt på samma sidan under **identifiera användare**, utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. I den **enkel inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen och klicka på **spara**.

    b. I den **utfärdar-URL för identitetsprovider** textrutan klistra in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen och klicka på **spara**.

    c. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **X.509-certifikat** och klickar på **spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-an-appraisd-test-user"></a>Skapa en Appraisd testanvändare

Om du vill aktivera Azure AD användare loggar in på Appraisd, de måste etableras i Appraisd. I Appraisd är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Appraisd som en administratör.

2. Längst upp till höger på sidan, klickar du på **inställningar** ikonen och gå sedan till **Administration centre**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. I verktygsfältet högst upp på sidan, klickar du på **personer**, gå sedan till **lägga till en ny användare**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. På den **lägga till en ny användare** utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. I **Förnamn** text, ange först namnet på användaren som **Britta**.

    b. I **efternamn** text anger efternamn för användaren som **simon**.

    c. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    d. Klicka på **Lägg till användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Appraisd.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. I listan med program väljer **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Appraisd i åtkomstpanelen du bör få automatiskt loggat in på ditt Appraisd program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
