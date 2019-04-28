---
title: 'Självstudier: Azure Active Directory-integrering med Workspot kontroll | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workspot kontroll.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098684"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Självstudier: Azure Active Directory-integrering med Workspot kontroll

I den här självstudien får du lära dig hur du integrerar Workspot kontroll med Azure Active Directory (AD Azure).

Integrera Workspot kontroll med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workspot kontroll.
- Du kan aktivera användarna att automatiskt få loggat in på Workspot kontroll (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Workspot kontroll, behöver du följande objekt:

- En Azure AD-prenumeration
- En kontroll för Workspot enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till Workspot kontroll från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workspot-control-from-the-gallery"></a>Lägger till Workspot kontroll från galleriet
Om du vill konfigurera integreringen av Workspot kontroll till Azure AD, som du behöver lägga till Workspot kontroll från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Workspot kontroll från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. I sökrutan skriver **Workspot kontroll**väljer **Workspot kontroll** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workspot kontroll baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarande Workspot kontrollen är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren Workspot kontroll upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workspot kontroll, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Workspot kontroll](#create-a-workspot-control-test-user)**  – du har en motsvarighet för Britta Simon Workspot kontrollen som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Workspot kontroll.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workspot kontroll:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Workspot kontroll** application integration markerar **enkel inloggning**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** intiated läge:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Workspot kontroll klienten supportteamet](mailto:support@workspot.com) att hämta dessa värden. 

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **Certifikat (Base64)** och spara det på datorn.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. På den **ställa in kontroller för Workspot** avsnittet, kopiera den lämpliga URL enligt dina behov.

    Observera att URL: en kan stå följande:

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. I ett annat webbläsarfönster, logga in på Workspot kontroll som en administratör.

8. I verktygsfältet högst upp på sidan, klickar du på **installationsprogrammet**, gå sedan till **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. På den **Security Assertion Markup Language Configuration** utför följande steg:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. I den **entitets-ID** textrutan klistra in värdet för **Azure Ad-identifierare** som du har kopierat från Azure-portalen.   

    b.In den **tjänst-URL för inloggning** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I den **Utloggning** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen. 

    d. Klicka på **uppdateringsfil** knappen för att ladda upp Base64-kodat certifikat som du har hämtat från Azure-portalen i X.509-certifikat.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-workspot-control-test-user"></a>Skapa en testanvändare Workspot kontroll

Om du vill aktivera Azure AD-användare att logga in på Workspot kontroll, måste de etableras i Workspot kontroll. Workspot kontroll är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Workspot kontroll som en administratör.

2. I verktygsfältet högst upp på sidan, klickar du på **användare**, gå sedan till **Lägg till användare**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. På den **lägga till en ny användare** utför följande steg:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren som **simon**.

    c. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon\@contoso.com**.

    d. Välj en lämplig användarroll från den **rollen** listrutan.

    e. Välj önskad användargrupp från den **grupp** listrutan.

    f. Klicka på **Lägg till användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workspot kontroll.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. I listan med program väljer **Workspot kontroll**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Workspot kontroll i åtkomstpanelen du bör få automatiskt loggat in på programmets Workspot kontroll.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
