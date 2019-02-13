---
title: 'Självstudier: Azure Active Directory-integrering med Evernote | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.openlocfilehash: 7cff5eaf49bb9b5af79e4673549ab6ee70394c0a
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100422"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Självstudier: Azure Active Directory-integrering med Evernote

I den här självstudien lär du dig att integrera Evernote med Azure Active Directory (AD Azure).
Integreringen av Evernote med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Evernote.
* Du kan göra så att dina användare automatiskt loggas in på Evernote (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att konfigurera Azure AD-integrering med Evernote:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Evernote-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Evernote har stöd för **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-evernote-from-the-gallery"></a>Lägga till Evernote från galleriet

För att konfigurera integrering av Evernote i Azure AD behöver du lägga till Evernote från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Evernote från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Evernote**, väljer **Evernote** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Evernote i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Evernote baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Evernote upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Evernote behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Evernote](#configure-evernote-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Evernote-testanvändare](#create-evernote-test-user)** – för att ha en motsvarighet till Britta Simon i Evernote som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Evernote:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Evernote** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med Evernote-domän och -URL:er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://www.evernote.com/saml2`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://www.evernote.com/Login.action`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Evernote** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-evernote-single-sign-on"></a>Konfigurera enkel inloggning för Evernote

1. I ett annat webbläsarfönster loggar du in på din Evernote-företagswebbplats som administratör.

2. Gå till **”Administratörskonsolen”**

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Från **”Administratörskonsolen”** går du till **”Säkerhet”** och väljer **”Enkel inloggning”**

    ![Inställning för enkel inloggning](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. Konfigurera följande värden:

    ![Inställning för certifikat](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Aktivera enkel inloggning:** Enkel inloggning är aktiverat som standard (klicka på **Inaktivera enkel inloggning** om du vill ta bort kravet på enkel inloggning)

    b. Klistra in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **SAML HTTP Request URL** (HTTP-begärande-URL för SAML).

    c. Öppna det certifikat som laddats ned från Azure AD i Anteckningar, kopiera innehållet, inklusive ”BEGIN CERTIFICATE” (början på certifikatet) och ”END CERTIFICATE” (slutet på certifikatet) och klistra in det i textrutan **X.509-certifikat**. 

    d. Klicka på **Spara ändringar**

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Evernote.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Evernote**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Evernote**.

    ![Länken för Evernote i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-evernote-test-user"></a>Skapa Evernote testanvändare

För att Azure AD-användare ska kunna logga in i Evernote måste de etableras till Evernote.  
När det gäller Evernote är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din Evernote-företagswebbplats som administratör.

2. Klicka på **”Administratörskonsolen”**.

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Från **”Administratörskonsolen”** går du till **”Lägg till användare”**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Lägg till gruppmedlemmar** i textrutan **E-post**, skriv e-postadressen för användarkontot och klicka på **Bjud in.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. När inbjudan har skickats får Azure Active Directory-kontoinnehavaren ett e-postmeddelande för att tacka ja till inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Evernote-panelen på åtkomstpanelen bör du automatiskt loggas in i Evernote som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

