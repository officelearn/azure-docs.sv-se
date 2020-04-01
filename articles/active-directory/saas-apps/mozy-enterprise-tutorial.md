---
title: 'Självstudiekurs: Azure Active Directory-integrering med Mozy Enterprise | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233497"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Självstudiekurs: Azure Active Directory-integrering med Mozy Enterprise

I den här självstudien får du lära dig hur du integrerar Mozy Enterprise med Azure Active Directory (Azure AD).
Genom att integrera Mozy Enterprise med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mozy Enterprise.
* Du kan aktivera dina användare så att de automatiskt loggas in på Mozy Enterprise (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Mozy Enterprise behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mozy Enterprise stöder **SP** initierade SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Lägga till Mozy Enterprise från galleriet

Om du vill konfigurera integreringen av Mozy Enterprise i Azure AD måste du lägga till Mozy Enterprise från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Mozy Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Mozy Enterprise**i sökrutan och välj **Mozy Enterprise** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Mozy Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Mozy Enterprise baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mozy Enterprise upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Mozy Enterprise måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mozy Enterprise-testanvändare](#create-mozy-enterprise-test-user)** – om du vill ha en motsvarighet till Britta Simon i Mozy Enterprise som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Mozy Enterprise:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Mozy** **Enterprise-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mozy Enterprise Domain och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Mozy Enterprise Client supportteam](https://support.mozy.com/) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Mozy Enterprise.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurera Mozy Enterprise enkel inloggning

1. I ett annat webbläsarfönster loggar du in på mozy Enterprise-företagets webbplats som administratör.

2. Klicka på **Autentiseringsprincip**i avsnittet **Konfiguration** .
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777314.png "Autentiseringsprincip")

3. Gör följande i avsnittet **Autentiseringsprincip:**
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777315.png "Autentiseringsprincip")
   
    a. Välj **katalogtjänst** som **leverantör**.
   
    b. Välj **Använd LDAP Push**.
   
    c. Klicka på fliken **SAML-autentisering**.
   
    d. Klistra in **inloggnings-URL**, som du har kopierat från Azure-portalen till textrutan **Autentiserings-URL.**
   
    e. Klistra in **Azure AD-identifierare**, som du har kopierat från Azure-portalen till **SAML-slutpunktstextrutan.**
   
    f. Öppna det nedladdade bas-64-kodade certifikatet i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in hela certifikatet i **SMS:en.**
   
    g. Välj **Aktivera SSO för administratörer om du vill logga in med sina nätverksautentiseringsuppgifter**.
   
    h. Klicka på **Spara ändringar**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Mozy Enterprise.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Mozy Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Mozy Enterprise**i programlistan .

    ![Mozy Enterprise-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mozy-enterprise-test-user"></a>Skapa Mozy Enterprise-testanvändare

För att Azure AD-användare ska kunna logga in på Mozy Enterprise måste de etableras i Mozy Enterprise. När det gäller Mozy Enterprise är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda andra Mozy Enterprise-verktyg eller API:er för att skapa Mozy Enterprise för att etablera Azure AD-användarkonton.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på din **Mozy** Enterprise-klient.

2. Klicka på **Användare**och sedan på **Lägg till ny användare**.
   
    ![Användare](./media/mozy-enterprise-tutorial/ic777317.png "Användare")
   
    >[!NOTE]
    >Alternativet **Lägg till ny användare** visas bara om **Mozy** har valts som leverantör under **Autentiseringsprincipen**. Om SAML-autentisering är konfigurerad läggs användarna automatiskt till vid sin första inloggning via Enkel inloggning.
    
3. Gör så här i den nya användardialogrutan:
   
    ![Lägg till användare](./media/mozy-enterprise-tutorial/ic777318.png "Lägg till användare")
   
    a. Välj en grupp i listan **Välj en grupp.**
   
    b. Välj en typ i listan **Vilken typ av användare.**
   
    c. Skriv namnet på Azure AD-användaren i textrutan **Användarnamn.**
   
    d. Skriv e-postadressen för Azure AD-användaren i **textrutan e-post.**
   
    e. Välj **Skicka e-post för användarinstruktioner**.
   
    f. Klicka på **Lägg till användare.**

     >[!NOTE]
     > När du har skapat användaren skickas ett e-postmeddelande till Azure AD-användaren som innehåller en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Mozy Enterprise på åtkomstpanelen bör du automatiskt loggas in på den Mozy Enterprise som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

