---
title: 'Självstudier: Azure Active Directory-katalogintegrering med Coralogix | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c25c57dec14a81e5bcfcfa044cf5d5302e1c88
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578840"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Självstudier: Azure Active Directory-katalogintegrering med Coralogix

I den här självstudien lär du dig att integrera Coralogix med Azure Active Directory (AD Azure).
När du integrerar Coralogix med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Coralogix.
* Du kan aktivera användarna att logga in automatiskt till Coralogix (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Coralogix behöver du följande objekt:

- En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
- En Coralogix single-sign-on aktiverat prenumeration. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Coralogix stöder SP-initierad SSO.

## <a name="add-coralogix-from-the-gallery"></a>Lägg till Coralogix från galleriet

Att konfigurera integrering av Coralogix i Azure AD, Lägg först till Coralogix från galleriet i din lista över hanterade SaaS-appar.

Om du vill lägga till Coralogix från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Coralogix**. Välj **Coralogix** i resultatfönstret och väljer sedan den **Lägg till** för att lägga till programmet.

     ![Coralogix i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Coralogix baserat på en testanvändare kallas Britta Simon.
För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i Coralogix.

Om du vill konfigurera och testa Azure AD enkel inloggning med Coralogix, slutföra följande byggblock:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Coralogix enkel inloggning](#configure-coralogix-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en testanvändare Coralogix](#create-a-coralogix-test-user) har en motsvarighet för Britta Simon i Coralogix som är länkad till en Azure AD-representation av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Coralogix, gör du följande:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Coralogix** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML** att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan rutan, gör följande:

    ![Information om Coralogix-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I den **inloggnings-URL** anger en URL med följande mönster: `https://<SUBDOMAIN>.coralogix.com`

    b. I den **identifierare (entitets-ID)** text, ange en URL, till exempel:
    
    `https://api.coralogix.com/saml/metadata.xml`

    eller

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Inloggnings-URL-värdet inte existerar. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta den [Coralogix klienten supportteamet](mailto:info@coralogix.com) att hämta värdet. Du kan även gå till mönster i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

5. Coralogix programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På den **ange in enkel inloggning med SAML** väljer den **redigera** knappen för att öppna den **användarattribut** dialogrutan.

    ![image](common/edit-attribute.png)

6. I den **användaranspråk** i avsnittet den **användarattribut** dialogrutan, redigerar du anspråk med hjälp av den **redigera** ikon. Du kan också lägga till anspråk med hjälp av **Lägg till nytt anspråk** konfigurera attributet som SAML-token som visas i föregående bild. Utför sedan följande steg:
    
    a. Välj den **redigeringsikonen** att öppna den **hantera användaranspråk** dialogrutan.

    ![bild](./media/coralogix-tutorial/tutorial_usermail.png) ![bild](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Välj **E-postadress** i listan **Välj format på namnidentifieraren**.

    c. Välj **user.mail** i listan **Källattribut**.

    d. Välj **Spara**.

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **Federation Metadata-XML**  från de angivna alternativen beroende på dina krav. Spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I den **konfigurera Coralogix** avsnittet, kopiera den lämpliga URL: er.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-coralogix-single-sign-on"></a>Konfigurera Coralogix enkel inloggning

Att konfigurera enkel inloggning på den **Coralogix** sida, skicka de hämtade **XML-Metadata för Federation** och kopieras URL: er från Azure portal för att den [Coralogix supportteamet](mailto:info@coralogix.com). De säkerställer att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Överst på skärmen väljer **ny användare**.

    ![Knappen Ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fältet, anger du ”brittasimon@yourcompanydomain.extension”. Exempel: i det här fallet, du kan ange ”brittasimon@contoso.com”.

    c. Välj den **Show lösenord** och anteckna värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Coralogix.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Coralogix**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Coralogix**.

    ![Länken Coralogix i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj den **Lägg till användare** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan. Klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Klicka sedan på den **Välj** längst ned på skärmen.

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="create-a-coralogix-test-user"></a>Skapa en Coralogix testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Coralogix. Arbeta med den [Coralogix supportteamet](mailto:info@coralogix.com) att lägga till användare i Coralogix-plattformen. Du måste skapa och aktivera användare innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av MyApps-portalen.

När du väljer panelen Coralogix MyApps-portalen kan bör du vara loggas in automatiskt till Coralogix. Mer information om MyApps-portalen finns i [vad är MyApps-portalen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

