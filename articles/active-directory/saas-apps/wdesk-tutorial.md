---
title: 'Självstudier: Azure Active Directory-integrering med Wdesk | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 71feb455457fdf75fb19121bac1927b42fe38b67
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265890"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Självstudier: Azure Active Directory-integrering med Wdesk

I den här självstudien får du lära dig hur du integrerar Wdesk med Azure Active Directory (AD Azure).
Integrera Wdesk med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Wdesk.
* Du kan aktivera användarna att vara automatiskt inloggad till Wdesk (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Wdesk, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Wdesk enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Wdesk **SP** och **IDP** -initierad SSO

## <a name="adding-wdesk-from-the-gallery"></a>Att lägga till Wdesk från galleriet

För att konfigurera integrering av Wdesk i Azure AD, som du behöver lägga till Wdesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Wdesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Wdesk**väljer **Wdesk** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Wdesk i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Wdesk baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Wdesk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wdesk, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Wdesk Single Sign-On](#configure-wdesk-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Wdesk](#create-wdesk-test-user)**  – du har en motsvarighet för Britta Simon i Wdesk som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Wdesk:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Wdesk** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Wdesk domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Wdesk domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du kan hämta dessa värden från WDesk portal när du konfigurerar SSO.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Wdesk** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-wdesk-single-sign-on"></a>Konfigurera Wdesk Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Wdesk som en administratör.

2. I nedre vänstra hörnet, klickar du på **Admin** och välj **kontoadministratören**:
 
     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. I Wdesk Admin, går du till **Security**, sedan **SAML** > **SAML-inställningar**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Under **allmänna inställningar**, kontrollera den **aktivera SAML enkel inloggning**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Under **tjänstinformation för providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiera den **inloggnings-URL** och klistra in den i **inloggnings-Url** textrutan på Azure-portalen.
   
      b. Kopiera den **Metadata_url** och klistra in den i **identifierare** textrutan på Azure-portalen.
       
      c. Kopiera den **konsument url** och klistra in den i **svars-Url** textrutan på Azure-portalen.
   
      d. Klicka på **spara** på Azure portal för att spara ändringarna.      

6. Klicka på **konfigurera inställningarna för IDP: N** att öppna **redigera inställningar för IDP: N** dialogrutan. Klicka på **Välj fil** att hitta den **Metadata.xml** fil som du sparade från Azure-portalen och sedan ladda upp den.
    
    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klicka på **Spara ändringar**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wdesk.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Wdesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Wdesk**.

    ![Länken Wdesk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wdesk-test-user"></a>Skapa Wdesk testanvändare

Om du vill aktivera Azure AD-användare att logga in på Wdesk, måste de etableras i Wdesk. I Wdesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Wdesk som en administratör.

2. Gå till **Admin** > **kontot Administratör**.

     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicka på **medlemmar** under **personer**.

4. Klicka på **Lägg till medlem** att öppna **Lägg till medlem** dialogrutan. 
   
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser1.png)  

5. I **användaren** text, anger användarnamnet för användaren som brittasimon@contoso.com och klicka på **Fortsätt** knappen.

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser3.png)

6.  Ange information som visas nedan:
  
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser4.png)
 
    a. I **e** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

7. Klicka på **spara medlemmen** knappen.  

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Wdesk i åtkomstpanelen, bör det vara loggas in automatiskt till Wdesk som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

