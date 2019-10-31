---
title: 'Självstudie: Azure Active Directory integrering med Coralogix | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158461"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Självstudie: Azure Active Directory integrering med Coralogix

I den här självstudien lär du dig att integrera Coralogix med Azure Active Directory (AD Azure).
När du integrerar Coralogix med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Coralogix.
* Du kan göra det möjligt för användarna att logga in automatiskt på Coralogix (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Coralogix behöver du följande objekt:

- En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderings period](https://azure.microsoft.com/pricing/free-trial/)på en månad.
- En Coralogix-aktiverad prenumeration med enkel inloggning. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Coralogix stöder SP-initierad SSO.

## <a name="add-coralogix-from-the-gallery"></a>Lägg till Coralogix från galleriet

Om du vill konfigurera integreringen av Coralogix i Azure AD måste du först lägga till Coralogix från galleriet i listan över hanterade SaaS-appar.

Gör så här om du vill lägga till Coralogix från galleriet:

1. I [Azure Portal](https://portal.azure.com)i det vänstra fönstret väljer du ikonen **Azure Active Directory** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Coralogix**i rutan Sök. Välj **Coralogix** i resultat fönstret och välj sedan knappen **Lägg till** för att lägga till programmet.

     ![Coralogix i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Coralogix baserat på en test användare som kallas Britta Simon.
För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Coralogix.

Om du vill konfigurera och testa enkel inloggning med Coralogix i Azure AD måste du först slutföra följande Bygg stenar:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Coralogix enkel inloggning](#configure-coralogix-single-sign-on) för att konfigurera inställningarna för enkel inloggning på program sidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Coralogix-testanvändare](#create-a-coralogix-test-user) för att få en motsvarighet till Britta Simon i Coralogix som är länkad till Azure AD-representation av användare.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Coralogix i Azure AD:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Coralogix** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialog rutan **grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Coralogix-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I rutan **inloggnings-URL** anger du en URL med följande mönster: `https://<SUBDOMAIN>.coralogix.com`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL, till exempel:
    
    `https://api.coralogix.com/saml/metadata.xml`

    eller

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL: en. Kontakta [Coralogix-klientens support team](mailto:info@coralogix.com) för att hämta värdet. Du kan också referera till mönstren i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

5. Coralogix-programmet förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** väljer du knappen **Redigera** för att öppna dialog rutan **användarattribut** .

    ![mallar](common/edit-attribute.png)

6. I avsnittet **användar anspråk** **i dialog rutan användarattribut redigerar** du anspråken med hjälp av **redigerings** ikonen. Du kan också lägga till anspråk genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-token-attributet som visas i föregående bild. Utför sedan följande steg:
    
    a. Välj **redigerings ikonen** för att öppna dialog rutan **hantera användar anspråk** .

    ![avbildning](./media/coralogix-tutorial/tutorial_usermail.png) ![avbildning](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Välj **E-postadress** i listan **Välj format på namnidentifieraren**.

    c. Välj **user.mail** i listan **Källattribut**.

    d. Välj **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen enligt dina krav. Spara den sedan på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera Coralogix** kopierar du lämpliga URL: er.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-coralogix-single-sign-on"></a>Konfigurera Coralogix enkel inloggning

Om du vill konfigurera enkel inloggning på **Coralogix** -sidan skickar du de hämtade **XML-metadata för federationsmetadata** och kopierade url: er från Azure Portal till [support teamet för Coralogix](mailto:info@coralogix.com). De säkerställer att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Överst på skärmen väljer du **ny användare**.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan **användare** utför du följande steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. Skriv "brittasimon@yourcompanydomain.extension" i fältet **användar namn** . I det här fallet kan du till exempel ange "brittasimon@contoso.com".

    c. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Coralogix.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Coralogix**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Coralogix**.

    ![Länken Coralogix i listan med program](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare** . Välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare. Klicka sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Klicka sedan på knappen **Välj** längst ned på skärmen.

7. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

### <a name="create-a-coralogix-test-user"></a>Skapa en Coralogix-test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Coralogix. Arbeta med [Coralogix support team](mailto:info@coralogix.com) för att lägga till användare i Coralogix-plattformen. Du måste skapa och aktivera användare innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen för Mina appar.

När du väljer panelen Coralogix på portalen för Mina appar, bör du loggas in automatiskt på Coralogix. Mer information om portalen för Mina appar finns i [Vad är portalen för Mina appar?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

