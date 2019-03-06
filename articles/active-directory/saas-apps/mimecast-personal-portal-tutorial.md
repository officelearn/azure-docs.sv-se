---
title: 'Självstudier: Azure Active Directory-integrering med Mimecast Personal Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd08de2711e8584b9f2bbe17716fe14400c8bf7
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871391"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Självstudier: Azure Active Directory-integrering med Mimecast Personal Portal

I den här självstudien får du lära dig hur du integrerar Mimecast Personal Portal med Azure Active Directory (Azure AD).
Integreringen av Mimecast Personal Portal med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Mimecast Personal Portal från Azure AD.
* Du kan konfigurera inställningar så att dina användare kan logga in automatiskt i Mimecast Personal Portal (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Mimecast Personal Portal behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Mimecast Personal Portal-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mimecast Personal Portal har stöd för **SP**-initierad enkel inloggning

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Lägga till Mimecast Personal Portal från galleriet

För att konfigurera integreringen av Mimecast Personal Portal med Azure AD måste du lägga till Mimecast Personal Portal från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mimecast Personal Portal från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Mimecast Personal Portal**, väljer **Mimecast Personal Portal** från resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Mimecast Personal Portal i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Mimecast Personal Portal baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mimecast Personal Portal upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Mimecast Personal Portal måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mimecast Personal Portal-testanvändare](#create-mimecast-personal-portal-test-user)**  – för att skapa en motsvarighet till Britta Simon i Mimecast Personal Portal som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Mimecast Personal Portal:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Mimecast Personal Portal** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Mimecast Personal Portal-domäner och -URL:er](common/sp-identifier-reply.png)

    a. I rutan **Inloggnings-URL** anger du en URL: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Till havs        | `https://jer-api.mimecast.com/login/saml`|

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | Region  |  Värde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Sydafrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australien       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Till havs        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. I textrutan **Svars-URL** anger du en URL: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Till havs        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [supportteamet för Mimecast Personal Portal-klienten](https://www.mimecast.com/customer-success/technical-support/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Mimecast Personal Portal** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Konfigurera Mimecast Personal Portal-prenumeration med enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga in i Mimecast Personal Portal som administratör.

2. Gå till **Tjänster \> Program**.
   
    ![Program](./media/mimecast-personal-portal-tutorial/ic794998.png "Program")

3. Klicka på **Autentiseringsprofiler**.
   
    ![Autentiseringsprofiler](./media/mimecast-personal-portal-tutorial/ic794999.png "Autentiseringsprofiler")

4. Klicka på **Ny autentiseringsprofil**.
   
    ![Ny autentiseringsprofil](./media/mimecast-personal-portal-tutorial/ic795000.png "Ny autentiseringsprofil")

5. Utför följande steg i avsnittet **Autentiseringsprofil**:
   
    ![Autentiseringsprofil](./media/mimecast-personal-portal-tutorial/ic795001.png "Autentiseringsprofil")
   
    a. Ange ett namn för din konfiguration i textrutan **Beskrivning**.
   
    b. Välj **Enforce SAML Authentication for Mimecast Personal Portal** (Kräv SAML-autentisering för Mimecast Personal Portal).
   
    c. Som **Provider** väljer du **Azure Active Directory**.
   
    d. I textrutan **Utfärdar-URL** klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
   
    e. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    f. I textrutan för **utloggnings-URL** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.

    g. Öppna ditt **base-64-kodade certifikat** som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **Identity Provider Certificate (Metadata)** (Certifikat för identitetsprovider, metadata).

    h. Välj **Tillåt enkel inloggning**.
   
    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Mimecast Personal Portal.

1. Välj **Företagsprogram** på Azure-portalen, välj **Alla program** och sedan **Mimecast Personal Portal**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Mimecast Personal Portal** i programlistan.

    ![Mimecast Personal Portal-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mimecast-personal-portal-test-user"></a>Skapa Mimecast Personal Portal-testanvändare

För att Azure AD-användare ska kunna logga in i Mimecast Personal Portal måste de vara etablerade i Mimecast Personal Portal. När det gäller Mimecast Personal Portal är etableringen en manuell åtgärd.

Du måste registrera en domän innan du kan skapa användare.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in i **Mimecast Personal Portal** som administratör.

2. Gå till **Kataloger \> Internt**.
   
    ![Kataloger](./media/mimecast-personal-portal-tutorial/ic795003.png "Kataloger")

3. Klicka på **Registrera ny domän**.
   
    ![Registrera ny domän](./media/mimecast-personal-portal-tutorial/ic795004.png "Registrera ny domän")

4. När den nya domänen har skapats klickar du på **Ny adress**.
   
    ![Ny adress](./media/mimecast-personal-portal-tutorial/ic795005.png "Ny adress")

5. I dialogrutan Ny adress utför du följande steg på ett giltigt Azure AD-konto som du vill etablera:
   
    ![Spara](./media/mimecast-personal-portal-tutorial/ic795006.png "Spara")
   
    a. I textrutan för **E-postadress** skriver du användarens **e-postadress** med formatet **BrittaSimon@contoso.com**.
    
    b. I textrutan **Globalt namn** anger du **användarnamnet** till **BrittaSimon**.

    c. I textrutorna **Lösenord** och **Bekräfta lösenord** skriver du användarens **Lösenord**.
   
    b. Klicka på **Spara**.

>[!NOTE]
>Du kan etablera Azure AD-användarkonton med hjälp av andra API:er eller genereringsverktyg för Mimecast Personal Portal-användarkonton som är tillgängliga för Mimecast Personal Portal.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Mimecast Personal Portal på åtkomstpanelen bör du loggas in automatiskt i Mimecast Personal Portal-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

