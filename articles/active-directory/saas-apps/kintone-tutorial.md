---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kintone | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 6786b44aca9ceed3cec5daf0f858a51e2dd12833
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227587"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Självstudiekurs: Azure Active Directory-integrering med Kintone

I den här självstudien får du lära dig hur du integrerar Kintone med Azure Active Directory (Azure AD).
Genom att integrera Kintone med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kintone.
* Du kan aktivera användarna automatiskt inloggade på Kintone (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kintone behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kintone enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kintone stöder **SP** initierade SSO

## <a name="adding-kintone-from-the-gallery"></a>Lägga till Kintone från galleriet

Om du vill konfigurera integreringen av Kintone i Azure AD måste du lägga till Kintone från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Kintone från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Kintone**i sökrutan och välj **Kintone** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Kintone i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Kintone baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kintone upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Kintone måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kintone Single Sign-On](#configure-kintone-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Kintone-testanvändare](#create-kintone-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kintone som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Kintone:

1. Välj Enkel inloggning på sidan Kintone-programintegrering på [Azure-portalen](https://portal.azure.com/). **Kintone** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Kintone-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.kintone.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Kintone Client supportteam](https://www.kintone.com/contact/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Kintone.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kintone-single-sign-on"></a>Konfigurera Enkel inloggning i Kintone

1. Logga in på **Kintone-företagets** webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **ikonen Inställningar**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Klicka på **Användare & Systemadministration**.

    ![Användare & systemadministration](./media/kintone-tutorial/ic785880.png "Användare & systemadministration")

1. Klicka på **Logga in**under ** \> Systemadministrationssäkerhet** .

    ![Logga in](./media/kintone-tutorial/ic785881.png "Inloggning")

1. Klicka på **Aktivera SAML-autentisering**.

    ![SAML-autentisering](./media/kintone-tutorial/ic785882.png "SAML-autentisering")

1. Gör följande i avsnittet SAML-autentisering:

    ![SAML-autentisering](./media/kintone-tutorial/ic785883.png "SAML-autentisering")

    a. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** om du vill ladda upp den nedladdade certifikatfilen från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kintone.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Kintone**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Kintone**i programlistan .

    ![Länken Kintone i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kintone-test-user"></a>Skapa Kintone-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Kintone måste de etableras i Kintone. När det gäller Kintone är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på **Kintone-företagets** webbplats som administratör.

1. Klicka på **ikonen Inställningar**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Klicka på **Användare & Systemadministration**.

    ![Användar- & systemadministration](./media/kintone-tutorial/ic785880.png "Användar- & systemadministration")

1. Klicka på **Avdelningar & användare under** **Användaradministration**.

    ![Avdelning & användare](./media/kintone-tutorial/ic785888.png "Avdelning & användare")

1. Klicka på **Ny användare**.

    ![Nya användare](./media/kintone-tutorial/ic785889.png "Nya användare")

1. Gör följande i avsnittet **Ny användare:**

    ![Nya användare](./media/kintone-tutorial/ic785890.png "Nya användare")

    a. Skriv ett **visningsnamn,** **inloggningsnamn,** **nytt lösenord,** **bekräfta lösenord,** **e-postadress**och annan information om ett giltigt Azure AD-konto som du vill etablera i relaterade textrutor.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa Kintone för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på kintonepanelen på Åtkomstpanelen bör du automatiskt loggas in på den Kintone som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
