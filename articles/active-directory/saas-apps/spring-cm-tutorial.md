---
title: 'Självstudiekurs: Azure Active Directory-integrering med SpringCM | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be4edfe3262ab74d903384eb00c0282acc7a3299
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867432"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudiekurs: Azure Active Directory-integrering med SpringCM

I den här självstudien får du lära dig hur du integrerar SpringCM med Azure Active Directory (Azure AD).
Genom att integrera SpringCM med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpringCM.
* Du kan aktivera dina användare automatiskt inloggade på SpringCM (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SpringCM behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SpringCM enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SpringCM stödjer **SP** initierad SSO

## <a name="adding-springcm-from-the-gallery"></a>Lägga till SpringCM från galleriet

Om du vill konfigurera integreringen av SpringCM i Azure AD måste du lägga till SpringCM från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SpringCM från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SpringCM**i sökrutan och välj **SpringCM** på resultatpanelen och klicka sedan på knappen **Lägg** till för att lägga till programmet.

    ![SpringCM i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med SpringCM baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SpringCM upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SpringCM måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpringCM Single Sign-On](#configure-springcm-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SpringCM-testanvändare](#create-springcm-test-user)** - om du vill ha en motsvarighet till Britta Simon i SpringCM som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SpringCM:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **F4CM-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SpringCM-domän och webbadresser med enkel inloggning](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SpringCM Client supportteam](https://knowledge.springcm.com/support) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SpringCM.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-springcm-single-sign-on"></a>Konfigurera SpringCM enkel inloggning

1. I ett annat webbläsarfönster loggar du in på **din SpringCM-företagswebbplats** som administratör.

1. Klicka på **Gå till**på menyn högst upp och klicka sedan på **Inställningar**och klicka sedan på **SAML SSO**i avsnittet **Kontoinställningar** .
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Gör följande i avsnittet Konfiguration av identitetsprovider:
   
    ![Konfiguration av identitetsprovider](./media/spring-cm-tutorial/ic797052.png "Konfiguration av identitetsprovider")
    
    a. Om du vill ladda upp ditt hämtade Azure Active Directory-certifikat klickar du på **Välj utfärdarcertifikat** eller **Ändra utfärdarcertifikat**.
    
    b. I textrutan **Utfärdare** klistrar du in **Azure AD-identifierare,** som du har kopierat från Azure-portalen.
    
    c. I textrutan **Tjänstprovider (SP) Initierad slutpunkt** klistrar du in **url-värde** för inloggning, som du har kopierat från Azure-portalen.
            
    d. Välj **SAML aktiverat** som **Aktiverat**.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SpringCM.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SpringCM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SpringCM**i programlistan .

    ![SpringCM-länken i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-springcm-test-user"></a>Skapa SpringCM-testanvändare

Om du vill att Azure Active Directory-användare ska kunna logga in på SpringCM måste de etableras i SpringCM. När det gäller SpringCM är etablering en manuell aktivitet.

> [!NOTE]
> Mer information finns i [Skapa och redigera en SpringCM-användare](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053). 

**Så här etablerar du ett användarkonto till SpringCM:**

1. Logga in på **din SpringCM-företagswebbplats** som administratör.

1. Klicka på **GOTO**och sedan på **ADRESSBOK**.
   
    ![Skapa användare](./media/spring-cm-tutorial/ic797054.png "Skapa användare")

1. Klicka på **Skapa användare**.

1. Välj en **användarroll**.

1. Välj **Skicka aktiveringsmeddelande**.

1. Skriv förnamn, efternamn och e-postadress för ett giltigt Azure Active Directory-användarkonto som du vill etablera i relaterade textrutor.

1. Lägg till användaren i en **säkerhetsgrupp**.

1. Klicka på **Spara**.

   > [!NOTE]
   > Du kan använda andra verktyg eller API:er för att skapa fÃ¤ger fÃ¤nderingar av FÃ¤nder fÃ¤nder fÃ¤nder fÃ¤nder fÃ¤ndear Azure AD-konton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SpringCM-panelen på åtkomstpanelen ska du automatiskt loggas in på den SpringCM som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

