---
title: 'Självstudiekurs: Azure Active Directory-integrering med Rally Software | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093179"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Självstudiekurs: Azure Active Directory-integrering med Rally Software

I den här självstudien får du lära dig hur du integrerar Rally Software med Azure Active Directory (Azure AD).
Genom att integrera Rallymjukvara med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rally Software.
* Du kan aktivera dina användare automatiskt inloggad på Rally Software (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Rally Software behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Rally Software enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rally Software stöder **SP** initierade SSO

## <a name="adding-rally-software-from-the-gallery"></a>Lägga till rallyprogramvara från galleriet

Om du vill konfigurera integreringen av Rally Software i Azure AD måste du lägga till Rally Software från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Rallymjukvara från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Rallymjukvara**i sökrutan och välj **Rallymjukvara** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Rally Software i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Rally Software baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Rally Software upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Rally Software måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera en inloggning för rallyprogram](#configure-rally-software-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare](#create-rally-software-test-user)** för Rally Software – om du vill ha en motsvarighet till Britta Simon i Rally Software som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Rally Software:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan Rally **Software-programintegration**. **Rally Software**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Rally Software Domain och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Rally Software Client support team](https://help.rallydev.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera rallyprogramvara.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rally-software-single-sign-on"></a>Konfigurera en inloggning för rallyprogram

1. Logga in på din **innehavare av Rally Software.**

2. Klicka på **Installationsprogrammet**i verktygsfältet högst upp och välj sedan **Prenumeration**.
   
    ![Prenumeration](./media/rally-software-tutorial/ic769531.png "Prenumeration")

3. Klicka på knappen **Åtgärd.** Välj **Redigera prenumeration** längst upp till höger i verktygsfältet.

4. Gör följande på dialogrutan **Prenumeration** och klicka sedan på **Spara & Stäng:**
   
    ![Autentisering](./media/rally-software-tutorial/ic769542.png "Autentisering")
   
    a. Välj **Rally- eller SSO-autentisering** från autentiseringsrullgarderskapet.

    b. Klistra in värdet för **Azure AD-identifierare**i URL-textrutan för **identitetsprovidern** som du har kopierat från Azure-portalen. 

    c. Klistra in värdet **för utloggnings-URL:en**i textrutan **SSO-utloggning** , som du har kopierat från Azure-portalen.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Rally Software.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Rally **Software**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Rallymjukvara**i programlistan .

    ![Länken Rallymjukvara i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rally-software-test-user"></a>Skapa testanvändare för rallyprogram

För att Azure AD-användare ska kunna logga in måste de etableras i Rally Software-programmet med sina Azure Active Directory-användarnamn.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din innehavare av Rally Software.

2. Gå till **Konfigurera \> ANVÄNDARE**och klicka sedan på + Lägg till **ny**.
   
    ![Användare](./media/rally-software-tutorial/ic781039.png "Användare")

3. Skriv namnet i textrutan Ny användare och klicka sedan på **Lägg till med information**.

4. I avsnittet **Skapa användare** utför du följande steg:
   
    ![Skapa användare](./media/rally-software-tutorial/ic781040.png "Skapa användare")

    a. Skriv namnet på användaren som **Brittsimon**i textrutan **Användarnamn** .
   
    b. I textrutan **E-postadress** anger du e-postmeddelandet för användaren som brittasimon@contoso.com.

    c. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    d. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

    e. Klicka på **Save & Close** (Spara och stäng).

   >[!NOTE]
   >Du kan använda andra verktyg för att skapa rallyprogram eller API:er som tillhandahålls av Rally Software för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Rallymjukvara på åtkomstpanelen ska du automatiskt loggas in på den rallyprogramvara som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

