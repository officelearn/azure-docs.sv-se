---
title: 'Självstudie: Azure Active Directory integrering med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nya Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: fd75cc392bb30d15b665190eef8fbea39d692ee3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160335"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Självstudie: Azure Active Directory integrering med nya Relic

I den här självstudien får du lära dig att integrera nya Relic med Azure Active Directory (Azure AD).
Genom att integrera nya Relic med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till nya Relic.
* Du kan göra det möjligt för användarna att logga in automatiskt till nya Relic (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med nya Relic behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Ny Relic-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* New Relic stöder **SP** -INITIERAd SSO

## <a name="adding-new-relic-from-the-gallery"></a>Lägga till nya Relic från galleriet

Om du vill konfigurera integrering av nya Relic i Azure AD måste du lägga till nya Relic från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till nya Relic från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du in **New Relic**, väljer **ny Relic** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Ny Relic i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med nya Relic baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i nya Relic upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med nya Relic måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera en ny Relic-enkel inloggning](#configure-new-relic-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en ny Relic-test](#create-new-relic-test-user)** för att få en motsvarighet till Britta Simon i New Relic som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med nya Relic:

1. I [Azure Portal](https://portal.azure.com/)på sidan **ny Relic** program integration väljer du **enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Ny information om enkel inloggning för Relic-domän och URL: er](common/sp-identifier.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – se till att du ersätter ditt eget nya konto-ID för Relic.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL: `rpm.newrelic.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera nya Relic** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-new-relic-single-sign-on"></a>Konfigurera ny Relic enkel inloggning

1. Logga in på din **nya Relic** företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **konto inställningar**på menyn högst upp.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797036.png "Konto inställningar")

3. Klicka på fliken **säkerhet och autentisering** och klicka sedan på fliken **enkel inloggning** .
   
    ![Enkel inloggning](./media/new-relic-tutorial/ic797037.png "för Aha!")

4. Utför följande steg på sidan SAML-dialog:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klicka på **Välj fil** för att ladda upp ditt hämtade Azure Active Directory certifikat.

    b. I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.
   
    c. I text rutan **Logga ut landnings-URL** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    d. Klicka på **Spara mina ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till nya Relic.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **ny Relic**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ny Relic**.

    ![Den nya Relic-länken i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-new-relic-test-user"></a>Skapa en ny Relic-test användare

För att Azure Active Directory användare ska kunna logga in på nya Relic måste de tillhandahållas i nya Relic. När det gäller nya Relic är etableringen en manuell uppgift.

**Utför följande steg för att etablera ett användar konto till en ny Relic:**

1. Logga in på den **nya Relic** företags webbplatsen som administratör.

2. Klicka på **konto inställningar**på menyn högst upp.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797040.png "Konto inställningar")

3. I fönstret **konto** till vänster klickar du på **Sammanfattning**och sedan på **Lägg till användare**.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797041.png "Konto inställningar")

4. I dialog rutan **aktiva användare** utför du följande steg:
   
    ![Aktiva användare](./media/new-relic-tutorial/ic797042.png "Aktiva användare")
   
    a. I text rutan **e-post** anger du e-postadressen till en giltig Azure Active Directory användare som du vill etablera.

    b. Som **roll** väljer du **användare**.

    c. Klicka på **Lägg till den här användaren**.

>[!NOTE]
>Du kan använda andra nya Relic-verktyg för användar konton eller API: er som tillhandahålls av New Relic för att etablera AAD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ny Relic på åtkomst panelen, bör du loggas in automatiskt på den nya Relic som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

