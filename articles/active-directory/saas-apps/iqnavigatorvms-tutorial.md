---
title: 'Självstudier: Azure Active Directory-integrering med virtuella IQNavigator-datorer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och virtuella IQNavigator-datorer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: dba50c984984363682efce1f09ef462b3c0c5def
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078489"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Självstudier: Azure Active Directory integrering med virtuella IQNavigator-datorer

I den här självstudien får du lära dig hur du integrerar virtuella IQNavigator-datorer med Azure Active Directory (Azure AD).
Integrering av virtuella IQNavigator-datorer med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till virtuella IQNavigator-datorer.
* Du kan låta dina användare vara automatiskt inloggade för att IQNavigator virtuella datorer (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med virtuella IQNavigator-datorer behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IQNavigator VM-aktiverad prenumeration för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Virtuella IQNavigator-datorer stöder **IDP** INITIERAd SSO

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Lägga till virtuella IQNavigator-datorer från galleriet

Om du vill konfigurera integreringen av virtuella IQNavigator-datorer i Azure AD måste du lägga till IQNavigator-VM: ar från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till virtuella IQNavigator-datorer från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **IQNAVIGATOR VM**: ar, väljer **IQNavigator VM** : ar från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![IQNavigator virtuella datorer i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med virtuella IQNavigator-datorer baserade på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i virtuella IQNavigator-datorer upprättas.

Om du vill konfigurera och testa enkel inloggning med IQNavigator-VM: ar för Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera IQNAVIGATOR VM Single Sign-on](#configure-iqnavigator-vms-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa IQNAVIGATOR VM test User](#create-iqnavigator-vms-test-user)** – om du vill ha en motsvarighet till Britta Simon i IQNAVIGATOR-VM: ar som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med virtuella IQNavigator-datorer:

1. I [Azure Portal](https://portal.azure.com/)på sidan **IQNavigator VM** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för IQNavigator VM-domäner och URL: er](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du in en URL: `iqn.com`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska svars-URL: en och det aktuella relä läget. Kontakta [IQNAVIGATOR VM client support team](https://www.beeline.com/support-iqn/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. IQNavigator-programmet förväntar sig värdet för unikt användar-ID i anspråket namn identifierare. Kunden kan mappa rätt värde för namn-ID-anspråket. I det här fallet har vi mappat användaren. UserPrincipalName för demonstrations syftet. Men enligt dina organisations inställningar bör du mappa rätt värde för det.

    ![image](common/edit-attribute.png)

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-iqnavigator-vms-single-sign-on"></a>Konfigurera IQNavigator VM-enkel inloggning

Om du vill konfigurera enkel inloggning på **IQNAVIGATOR VM** -sidan måste du skicka **URL: en** för appens Federations-metadata till [IQNavigator VM support team](https://www.beeline.com/support-iqn/). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till IQNavigator-VM: ar.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **IQNavigator virtuella datorer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **virtuella datorer för IQNavigator**.

    ![IQNavigator VM-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-iqnavigator-vms-test-user"></a>Skapa IQNavigator VM test User

I det här avsnittet skapar du en användare som heter Britta Simon i IQNavigator VM: ar. Arbeta med [IQNAVIGATOR VM support team](https://www.beeline.com/support-iqn/) för att lägga till användare i IQNavigator VM-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen virtuella IQNavigator-datorer i åtkomst panelen, bör du loggas in automatiskt på de IQNavigator-VM: ar som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)