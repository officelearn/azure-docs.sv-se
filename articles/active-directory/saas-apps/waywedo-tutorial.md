---
title: 'Självstudier: Azure Active Directory-integrering med sätt vi göra | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hur vi gör.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565550"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Självstudier: Azure Active Directory-integrering med sätt som vi gör

I den här självstudien får du lära dig hur du integrerar sätt vi göra med Azure Active Directory (AD Azure).
Integrera sätt vi göra med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till sätt som vi gör.
* Du kan aktivera användarna att logga in automatiskt sätt vi göra (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med sätt vi göra, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Sätt som vi enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Hur vi gör stöder **SP** -initierad SSO

* Hur vi gör stöder **Just In Time** etableringen av användare

## <a name="adding-way-we-do-from-the-gallery"></a>Att lägga till sätt vi göra från galleriet

För att konfigurera integrering av sätt vi göra i Azure AD, som du behöver lägga till sätt vi göra från galleriet i din lista över hanterade SaaS-appar.

**Lägg till sätt vi göra från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **sätt vi göra**väljer **sätt vi göra** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Sätt som vi gör i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med sätt som vi gör baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i sätt vi göra upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med sätt vi göra, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera sättet som vi enkel inloggning](#configure-way-we-do-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa sätt som vi testanvändare](#create-way-we-do-test-user)**  – du har en motsvarighet för Britta Simon på sätt som vi gör som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med sätt vi göra:

1. I den [Azure-portalen](https://portal.azure.com/)på den **sätt vi göra** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Sätt vi göra domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [sätt vi gör klienten supportteamet](mailto:support@waywedo.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **ställa in hur vi gör** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-way-we-do-single-sign-on"></a>Konfigurera sättet som vi enkel inloggning

1. I ett annat webbläsarfönster, loggar du in hur vi gör som en administratör.

2. Klicka på den **person ikonen** i det övre högra hörnet på en sida i sätt som vi gör, och klicka på **konto** i den nedrullningsbara menyn.

    ![Hur vi gör konto](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Klicka på den **menyikonen** att öppna den push navigeringsmenyn och klicka på **Single Sign On**.

    ![Sätt som vi gör enda](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. På den **installationsprogrammet för enkel inloggning** utför följande steg:

    ![Sätt som vi gör spara](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klicka på den **aktivera enkel inloggning** växla till **Ja** att aktivera enkel inloggning.

    b. I den **namn för enkel inloggning** textrutan, ange ditt namn.

    c. I den **entitets-ID** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    d. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    e. Ladda upp certifikatet genom att klicka på den **Välj** bredvid knappen **certifikat**.

    f. **Valfria inställningar** -
    
    * Aktivera lösenord – när det här alternativet är inaktiverat, regelbundna lösenordet fungerar för hur vi gör så att användarna bara kan använda enkel inloggning.

    * Aktivera automatisk etablering – när den är aktiverad, e-postadressen används för att inloggnings-automatiskt ska jämföras med en lista över användare i sätt som vi gör. Om e-postadressen inte matchar en aktiv användare på sätt som vi gör det lägger automatiskt till ett nytt användarkonto för den person som loggar in, begär eventuell information som saknas.

      > [!NOTE]
      > Användare som har lagts till via enkel inloggning har lagts till som allmänt användare och tilldelats inte en roll i systemet. En administratör kan gå in och ändra deras säkerhetsroll som en redigerare eller administratör och kan också tilldela en eller flera Organisationsschema roller. 

    g. Klicka på **spara** att spara dina inställningar.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till sätt som vi gör.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **sätt vi göra**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **sätt vi göra**.

    ![Länken sätt som vi gör i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-way-we-do-test-user"></a>Skapa sätt vi göra testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i sätt som vi gör. Hur vi gör stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns på sätt som vi gör, skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [sätt vi gör klienten supportteamet](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen sätt som vi gör i åtkomstpanelen, bör det vara loggas in automatiskt till de sätt vi gör som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

