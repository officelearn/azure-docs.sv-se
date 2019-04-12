---
title: 'Självstudier: Azure Active Directory-integrering med SpringCM | Microsoft Docs'
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
ms.date: 04-08-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f63fe3b006b704291da1613627019e5866cd32
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500098"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudier: Azure Active Directory-integrering med SpringCM

I den här självstudien får du lära dig hur du integrerar SpringCM med Azure Active Directory (AD Azure).
Integrera SpringCM med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpringCM.
* Du kan aktivera användarna att vara automatiskt inloggad till SpringCM (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SpringCM, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SpringCM enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SpringCM **SP** -initierad SSO

## <a name="adding-springcm-from-the-gallery"></a>Att lägga till SpringCM från galleriet

För att konfigurera integrering av SpringCM i Azure AD, som du behöver lägga till SpringCM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpringCM från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program, klicka på den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SpringCM**väljer **SpringCM** från panelen resultatet klickar på **Lägg till** för att lägga till programmet.

    ![SpringCM i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SpringCM baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SpringCM upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SpringCM, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpringCM Single Sign-On](#configure-springcm-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare SpringCM](#create-springcm-test-user)**  – du har en motsvarighet för Britta Simon i SpringCM som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SpringCM:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SpringCM** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SpringCM domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SpringCM klienten supportteamet](https://knowledge.springcm.com/support) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **konfigurera SpringCM** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-springcm-single-sign-on"></a>Konfigurera SpringCM Single Sign-On

1. I ett annat webbläsarfönster, loggar du in på ditt **SpringCM** företagets plats som administratör.

1. Klicka på menyn längst upp **gå till**, klickar du på **inställningar**, och klicka sedan på **kontoinställningar** klickar du på **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Utför följande steg i konfigurationsavsnittet Identity-providern:
   
    ![Identitet providerkonfigurationen](./media/spring-cm-tutorial/ic797052.png "identitet Providerkonfiguration")
    
    a. Om du vill ladda upp din hämtade Azure Active Directory-certifikatet klickar du på **Välj utfärdarcertifikatet** eller **ändra utfärdarcertifikatet**.
    
    b. I den **utfärdare** textrutan klistra in **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.
    
    c. I den **slutpunkten för Service Provider (SP) initieras** textrutan klistra in **inloggnings-URL** värde, som du har kopierat från Azure-portalen.
            
    d. Välj **SAML aktiverat** som **aktivera**.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpringCM.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SpringCM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SpringCM**.

    ![Länken SpringCM i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-springcm-test-user"></a>Skapa SpringCM testanvändare

Om du vill aktivera Azure Active Directory-användare att logga in på SpringCM, måste de etableras i SpringCM. När det gäller SpringCM är etablering en manuell aktivitet.

> [!NOTE]
> Mer information finns i [skapar och redigerar en SpringCM användare](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Om du vill konfigurera ett användarkonto till SpringCM, utför du följande steg:**

1. Logga in på din **SpringCM** företagets plats som administratör.

1. Klicka på **GOTO**, och klicka sedan på **ADRESSBOKEN**.
   
    ![Skapa användare](./media/spring-cm-tutorial/ic797054.png "Skapa användare")

1. Klicka på **Skapa användare**.

1. Välj en **användarrollen**.

1. Välj **skickar Aktiveringsmeddelandet**.

1. Ange den förnamn, efternamn och e-postadress för ett giltigt Azure Active Directory-användarkonto som du vill etablera till relaterade textrutor.

1. Lägga till användare i en **säkerhetsgrupp**.

1. Klicka på **Spara**.

   > [!NOTE]
   > Du kan använda alla andra SpringCM användare konto verktyg för att skapa eller API: er som tillhandahålls av SpringCM att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SpringCM i åtkomstpanelen, bör det vara loggas in automatiskt till SpringCM som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

