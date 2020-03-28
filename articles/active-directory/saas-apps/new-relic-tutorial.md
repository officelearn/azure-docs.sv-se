---
title: 'Självstudiekurs: Azure Active Directory-integrering med Ny Relik | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic.
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
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233522"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Självstudiekurs: Azure Active Directory-integrering med ny relik

I den här självstudien får du lära dig hur du integrerar Ny relik med Azure Active Directory (Azure AD).
Genom att integrera ny relik med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Ny relik.
* Du kan aktivera dina användare automatiskt inloggade på Ny relik (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Ny relik behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Ny relik enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Ny Relic stöder **SP** initierade SSO

## <a name="adding-new-relic-from-the-gallery"></a>Lägga till ny relik från galleriet

Om du vill konfigurera integreringen av Ny relik i Azure AD måste du lägga till Ny relik från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Ny relik från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Ny relik**i sökrutan och välj **Ny relik** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Ny relik i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Ny relik baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Ny relik upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Ny relik måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ny relik enkel inloggning](#configure-new-relic-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ny reliktestanvändare](#create-new-relic-test-user)** – om du vill ha en motsvarighet till Britta Simon i Ny relik som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Ny relik:

1. Välj [Azure portal](https://portal.azure.com/) **Enkel inloggning**på sidan **Ny återlikingsprogram.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Ny relikdomän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. Skriv en URL med följande mönster i textrutan `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` Logga in på **webbadress:** - Var noga med att ersätta ditt eget nya tilläggskonto-ID.

    b. Skriv en URL i textrutan **Identifierare (enhets-ID):**`rpm.newrelic.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera ny relik.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-new-relic-single-sign-on"></a>Konfigurera ny relik enkel inloggning

1. I ett annat webbläsarfönster loggar du in på webbplatsen **För nytt relikföretag** som administratör.

2. Klicka på **Kontoinställningar**högst upp.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797036.png "Kontoinställningar")

3. Klicka på fliken **Säkerhet och autentisering** och klicka sedan på fliken **Enkel inloggning.**
   
    ![Enkel inloggning](./media/new-relic-tutorial/ic797037.png "för Aha!")

4. Gör följande på dialogsidan för SAML:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klicka på **Välj fil** om du vill ladda upp ditt hämtade Azure Active Directory-certifikat.

    b. Klistra in värdet för **inloggnings-URL**i textrutan **För fjärrinloggning** , som du har kopierat från Azure-portalen.
   
    c. Klistra in värdet **för url för utloggning**i textrutan **Logout landing URL** , som du har kopierat från Azure-portalen.

    d. Klicka på **Spara ändringarna**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Ny relik.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Ny relik**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Ny relik**i programlistan .

    ![Länken Ny relik i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-new-relic-test-user"></a>Skapa en ny reliktestanvändare

För att Azure Active Directory-användare ska kunna logga in på Ny relik måste de etableras i Ny relik. När det gäller Ny relik är etablering en manuell aktivitet.

**Så här etablerar du ett användarkonto till Ny relik:**

1. Logga in på webbplatsen **Nya Reliker** som administratör.

2. Klicka på **Kontoinställningar**högst upp.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797040.png "Kontoinställningar")

3. Klicka på **Sammanfattning**i **fönstret Konto** till vänster och klicka sedan på Lägg **till användare**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797041.png "Kontoinställningar")

4. Gör följande i dialogrutan **Aktiva användare:**
   
    ![Aktiva användare](./media/new-relic-tutorial/ic797042.png "Aktiva användare")
   
    a. Skriv e-postadressen till en giltig Azure Active Directory-användare som du vill etablera i **textrutan e-post.**

    b. Som **roll** väljer **du Användare**.

    c. Klicka på **Lägg till den här användaren**.

>[!NOTE]
>Du kan använda andra verktyg för att skapa nya reliker eller API:er som tillhandahålls av Nya Reliken för att etablera Azure AD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Ny relik på åtkomstpanelen ska du automatiskt loggas in på den nya reliken som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

