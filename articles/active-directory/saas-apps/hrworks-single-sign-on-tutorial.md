---
title: 'Självstudie: Azure Active Directory integrering med HRworks enkel inloggning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HRworks enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: a36266c14531f935779266829402392dc4a03411
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706002"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Självstudie: Azure Active Directory integrering med HRworks enkel inloggning

I den här självstudien får du lära dig att integrera HRworks enkel inloggning med Azure Active Directory (Azure AD).
Att integrera HRworks enkel inloggning med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till HRworks-enkel inloggning.
* Du kan göra det möjligt för användarna att logga in automatiskt till HRworks enkel inloggning (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med HRworks enkel inloggning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* HRworks för enkel inloggning med enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* HRworks enkel inloggning stöder **SP** -INITIERAd SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Lägga till HRworks enkel inloggning från galleriet

Om du vill konfigurera integrering av HRworks enkel inloggning i Azure AD måste du lägga till HRworks enkel inloggning från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till HRworks-enkel inloggning från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **HRworks enkel inloggning**, väljer **HRworks enkel inloggning** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![HRworks enkel inloggning i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med HRworks-inloggning baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i HRworks enkel inloggning.

Om du vill konfigurera och testa enkel inloggning med HRworks med enkel inloggning i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera HRworks](#configure-hrworks-single-sign-on-single-sign-on)** enkel inloggning med enkel inloggning för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HRworks-test för enkel inloggning](#create-hrworks-single-sign-on-test-user)** för att få en motsvarighet till Britta Simon i HRworks enkel inloggning som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med HRworks enkel inloggning i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på **HRworks för enkel inloggning** på program sidan.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för HRworks-domän och URL-adresser](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [support teamet för HRworks-klienten för enkel inloggning](https://www.hrworks.de/dienstleistungen/support/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera HRworks enkel inloggning** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Konfigurera enkel inloggning med HRworks enkel inloggning

1. Logga in på HRworks enkel inloggning som administratör i ett annat webbläsarfönster.

2. Klicka på **administratör** > **grunderna** > **säkerhet** > **enkel inloggning på** den vänstra sidan av meny raden och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Markera kryss rutan **Använd enkel inloggning** .

    b. Välj **XML-metadata** som indata- **metod för metadata**.

    c. Välj **individuell NameID-ID** som **värde för NameID**.

    d. I anteckningar öppnar du XML-metadata som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i text rutan **metadata** .

    e. Klicka på **Save** (Spara).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** anger du användar namnet som BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till HRworks enkel inloggning.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **HRworks enkel inloggning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **HRworks enkel inloggning**.

    ![HRworks-länken för enkel inloggning i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Skapa test användare av HRworks-enkel inloggning

Om du vill aktivera Azure AD-användare loggar du in på HRworks enkel inloggning, de måste vara etablerade i HRworks enkel inloggning. I HRworks enkel inloggning är etableringen en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på HRworks enkel inloggning som administratör.

2. Klicka på **administratör** > **personer** > **personer** > **ny person** från vänster sida av meny raden.

     ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. Klicka på **Nästa**på popup-fönstret.

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. I popup-fönstret **Skapa ny person med ett land för juridiska villkor** fyller du i respektive information som **förnamn**, **efter namn** och klickar på **skapa**.
    
    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för enkel inloggning på HRworks i åtkomst panelen, bör du loggas in automatiskt på den HRworks enkel inloggning som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

