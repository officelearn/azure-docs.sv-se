---
title: 'Självstudiekurs: Azure Active Directory-integrering med Questetra BPM Suite | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093365"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Självstudiekurs: Azure Active Directory-integrering med Questetra BPM Suite

I den här självstudien får du lära dig hur du integrerar Questetra BPM Suite med Azure Active Directory (Azure AD).
Genom att integrera Questetra BPM Suite med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Questetra BPM Suite.
* Du kan aktivera dina användare automatiskt inloggade på Questetra BPM Suite (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Questetra BPM Suite behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Questetra BPM Suite stöder **SP** initierade SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Lägga till Questetra BPM Suite från galleriet

Om du vill konfigurera integreringen av Questetra BPM Suite i Azure AD måste du lägga till Questetra BPM Suite från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Questetra BPM Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Questetra BPM Suite**, väljer **Questetra BPM Suite** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

     ![Questetra BPM Suite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Questetra BPM Suite baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Questetra BPM Suite upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Questetra BPM Suite måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Questetra BPM Suite Enkel inloggning](#configure-questetra-bpm-suite-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Questetra BPM Suite-testanvändare](#create-questetra-bpm-suite-test-user)** – om du vill ha en motsvarighet till Britta Simon i Questetra BPM Suite som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Questetra BPM Suite:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Questetra BPM** **Suite-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Questetra BPM Suite Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Du kan få dessa värden från **SP Information** avsnitt på din **Questetra BPM Suite** företagswebbplats, som förklaras senare i handledningen eller kontakta [Questetra BPM Suite Client supportteam](https://www.questetra.com/contact/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Questetra BPM Suite.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurera Questetra BPM Suite Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Webbplats för **Questetra BPM Suite** som administratör.

2. Klicka på **Systeminställningar**högst upp. 
   
    ![Enkel azure AD-inloggning][10]

3. Om du vill öppna sidan **SingleSignOnSAML** klickar du på **SSO (SAML).** 
   
    ![Enkel azure AD-inloggning][11]

4. På webbplatsen **Questetra BPM Suite** gör du följande i avsnittet **SP-information** på webbplatsen För Projekt- och BPM-paket:

    a. Kopiera **ACS-URL:en**och klistra sedan in den i textrutan **Logga in url** i avsnittet Grundläggande **SAML-konfiguration** från Azure-portalen.
    
    b. Kopiera **entitets-ID:t**och klistra sedan in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** från Azure-portalen.

5. På din webbplats för **Questetra BPM Suite** gör du följande steg: 
   
    ![Konfigurera enkel inloggning][15]
   
    a. Välj **Aktivera enkel inloggning**.
   
    b. I **entitets-ID-textrutan** klistrar du in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
    
    c. Klistra in värdet för **inloggningsadressen** som du har kopierat från **Azure-portalen i textrutan för inloggningssidan.**
    
    d. Klistra in värdet för url-URL för **utloggning** som du har kopierat från Azure-portalen i textrutan för **utloggningssida.**
    
    e. Skriv `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`i textrutan **NameID-format** .

    f. Öppna ditt **Base-64-kodade** certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **för valideringscertifikat.** 

    g. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Questetra BPM Suite.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Questetra BPM Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Questetra BPM Suite**i programlistan .

    ![Länken Questetra BPM Suite i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-questetra-bpm-suite-test-user"></a>Skapa Questetra BPM Suite-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Questetra BPM Suite.

**Så här skapar du en användare som heter Britta Simon i Questetra BPM Suite:**

1. Logga in på din Uppdrags BPM Suite-företagswebbplats som administratör.

2. Gå till **Systeminställningar > användarlista > ny användare**.
 
3. Gör följande i dialogrutan Ny användare: 
   
    ![Skapa testanvändare][300] 
   
    a. Skriv **namnet** på användaren britta.simon@contoso.comi textrutan **Namn** .
   
    b. Skriv **e-post till** användaren britta.simon@contoso.comi **textrutan e-post** .
   
    c. Skriv ett **lösenord** för användaren i textrutan **Lösenord.**
    
    d. Klicka på **Lägg till ny användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Questetra BPM Suite på åtkomstpanelen bör du automatiskt loggas in på Questetra BPM Suite som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png