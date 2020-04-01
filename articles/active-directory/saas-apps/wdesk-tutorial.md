---
title: 'Självstudiekurs: Azure Active Directory-integrering med Wdesk | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985575"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Wdesk

I den här självstudien får du lära dig hur du integrerar Wdesk med Azure Active Directory (Azure AD). När du integrerar Wdesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Wdesk.
* Gör att användarna automatiskt loggas in på Wdesk med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Wdesk enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Wdesk stöder **SP** och **IDP** initierade SSO
* När du har konfigurerat Wdesk kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Lägga till Wdesk från galleriet

Om du vill konfigurera integreringen av Wdesk i Azure AD måste du lägga till Wdesk från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Wdesk** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Wdesk** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Wdesk baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Wdesk upprättas.

Om du vill konfigurera och testa Azure AD SSO med Wdesk slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Wdesk SSO](#configure-wdesk-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Wdesk-testanvändare](#create-wdesk-test-user)** – om du vill ha en motsvarighet till B.Simon i Wdesk som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Wdesk:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Wdesk-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Wdesk-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Wdesk-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får dessa värden från WDesk-portalen när du konfigurerar SSO.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Wdesk.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Wdesk.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Wdesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Wdesk**i programlistan .

    ![Wdesk-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-wdesk-sso"></a>Konfigurera Wdesk SSO

1. Logga in på Wdesk som säkerhetsadministratör i ett annat webbläsarfönster.

2. Längst ned till vänster klickar du på **Admin** och väljer **Kontoadministratör:**
 
     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. I Wdesk Admin, navigera till **säkerhet**, sedan **SAML** > **SAML-inställningar:**

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Under **Allmänna inställningar**kontrollerar du aktivera **SIML-enkel inloggning:**

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Under **Information om tjänsteprovider**utför du följande steg:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiera **inloggningsadressen** och klistra in den i textrutan Logga in url på **Azure-portalen.**
   
      b. Kopiera **metadataadressen** och klistra in den i **textrutan Identifierare** på Azure-portalen.
       
      c. Kopiera **konsument-url:en** och klistra in den i textrutan **Svara url** på Azure-portalen.
   
      d. Klicka på **Spara** på Azure-portalen för att spara ändringarna.      

6. Klicka på **Konfigurera IdP-inställningar** för att öppna dialogrutan **Redigera IdP-inställningar.** Klicka på **Välj fil** om du vill hitta filen **Metadata.xml** som du sparade från Azure-portalen och sedan ladda upp den.
    
    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klicka på **Spara ändringar**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Skapa Wdesk-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Wdesk måste de etableras i Wdesk. I Wdesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Wdesk som säkerhetsadministratör.

2. Navigera till**administratörsadministratör för administratörskonto** **Admin** > .

     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicka på **Medlemmar** under **Personer**.

4. Klicka nu på **Lägg till medlem** för att öppna Dialogrutan Lägg till **medlem.** 
   
    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. I textrutan **Användare** anger du brittasimon@contoso.com användarnamnet för användaren som och klickar på **Knappen Fortsätt.**

    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Fyll i informationen som visas nedan:
  
    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. I textrutan **E-post** anger du brittasimon@contoso.come-postmeddelandet för användaren som .

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

7. Klicka på **Knappen Spara medlem.**  

    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Wdesk-panelen på åtkomstpanelen ska du automatiskt loggas in på den Wdesk som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)