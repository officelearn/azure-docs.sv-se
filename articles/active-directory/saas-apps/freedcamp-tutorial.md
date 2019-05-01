---
title: 'Självstudier: Azure Active Directory-integrering med Freedcamp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39aeaa3edd3682272d63f6edca1dd2a341c00ba5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927060"
---
# <a name="tutorial-azure-active-directory-integration-with-freedcamp"></a>Självstudier: Azure Active Directory-integrering med Freedcamp

I den här självstudien får du lära dig hur du integrerar Freedcamp med Azure Active Directory (AD Azure).
Integrera Freedcamp med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Freedcamp.
* Du kan aktivera användarna att vara automatiskt inloggad till Freedcamp (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Freedcamp, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Freedcamp enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Freedcamp **SP och IDP** -initierad SSO

## <a name="adding-freedcamp-from-the-gallery"></a>Att lägga till Freedcamp från galleriet

För att konfigurera integrering av Freedcamp i Azure AD, som du behöver lägga till Freedcamp från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Freedcamp från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Freedcamp**väljer **Freedcamp** resultatet panelen och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Freedcamp i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Freedcamp baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Freedcamp upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Freedcamp, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Freedcamp Single Sign-On](#configure-freedcamp-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Freedcamp](#create-freedcamp-test-user)**  – du har en motsvarighet för Britta Simon i Freedcamp som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Freedcamp:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Freedcamp** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen **Redigeringa** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Freedcamp domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Freedcamp domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Användare kan också ange url-värden med avseende på sin egen kund-domän och de kanske inte nödvändigtvis av mönstret `freedcamp.com`, de kan ange ett kund domän specifikt värde, specifik för deras programinstans. Du kan också kontakta [Freedcamp klienten supportteamet](mailto:devops@freedcamp.com) mer information om url-mönster.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Freedcamp** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-freedcamp-single-sign-on"></a>Konfigurera Freedcamp Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Freedcamp som en administratör.

2. I det övre högra hörnet på sidan, klickar du på **profil** och gå sedan till **mitt konto**.

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config01.png)

3. Från vänster sida av menyraden klickar du på **SSO** på den **Your SSO anslutningar** sidan utför följande steg:

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config02.png)

    a. I den **rubrik** text skriver du rubriken.

    b. I den **entitets-ID** textrutan, klistra in den **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    c. I den **inloggnings-URL** textrutan, klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    d. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    e. Klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Freedcamp.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Freedcamp**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Freedcamp**.

    ![Länken Freedcamp i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-freedcamp-test-user"></a>Skapa Freedcamp testanvändare

Om du vill aktivera Azure AD-användare att logga in på Freedcamp, måste de etableras i Freedcamp. I Freedcamp är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster, loggar du in Freedcamp som en administratör.

2. I det översta toright hörnet på sidan, klickar du på **profil** och gå sedan till **hantera System**.

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config03.png)

3. Till höger på sidan Hantera System utför du följande steg:

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config04.png)

    a. Klicka på **Lägg till eller be användare**.

    b. I den **e-post** text, ange den e-postadressen för användaren som `Brittasimon@contoso.com`.

    c. Klicka på **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Freedcamp i åtkomstpanelen, bör det vara loggas in automatiskt till Freedcamp som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

