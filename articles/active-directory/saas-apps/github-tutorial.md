---
title: 'Självstudier: Azure Active Directory-integrering med GitHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25540d1f26fa6021ef05108f9743e77a6184f3b3
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426332"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Självstudier: Azure Active Directory-integrering med GitHub

I den här självstudien lär du dig att integrera GitHub med Azure Active Directory (AD Azure).
Genom att integrera GitHub med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till GitHub.
* Du kan göra så att dina användare automatiskt loggas in på GitHub (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med GitHub behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GitHub stöder **IDP**-initierad enkel inloggning

* GitHub stöder [**automatisk** användaretablering](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet

För att konfigurera integreringen av GitHub till Azure AD behöver du lägga till GitHub från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till GitHub från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **GitHub**väljer **GitHub.com** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![GitHub i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med GitHub baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i GitHub upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med GitHub behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för GitHub](#configure-github-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa GitHub-testanvändare](#create-github-test-user)** – för att ha en motsvarighet för Britta Simon i GitHub som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med GitHub:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **GitHub**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![GitHub-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://github.com/orgs/<entity-id>/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observera att detta inte är de verkliga värdena. Du behöver uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Gå till GitHub-administrationsavsnittet för att hämta dessa värden.

5. GitHub-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. GitHub-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera GitHub** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-github-single-sign-on"></a>Konfigurera enkel inloggning för GitHub

1. I ett annat webbläsarfönster loggar du in på din GitHub-organisationsplats som administratör.

2. Gå till **Inställningar** och klicka på **Säkerhet**

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Markera rutan **Aktivera SAML-autentisering**. Då visas konfigurationsfälten för enkel inloggning. Använd sedan URL-värdet för enkel inloggning för att uppdatera URL:en för enkel inloggning i Azure AD-konfiguration.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Konfigurera följande fält:

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. I textrutan **Inloggnings-URL** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **Issuer** (Utfärdare) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i Anteckningar och klistra in innehållet i textrutan **Offentligt certifikat**.

    d. Klicka på ikonen **Redigera** för att redigera **Signature Method** (Signaturmetod) och **Digest Method** (Hashmetod) från **RSA-SHA1** och **SHA1** till **RSA-SHA256** och **SHA256** enligt nedan.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klicka på **Testa SAML-konfiguration** för att bekräfta att inga verifieringsfel eller andra fel inträffade under enkel inloggning.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klicka på **Spara**

> [!NOTE]
> Enkel inloggning i GitHub autentiserar till en specifik organisation i GitHub och ersätter inte autentisering av själva GitHub. Om användarens github.com-session har upphört att gälla kan det därför hända att du blir ombedd att autentisera med ID/lösenord för GitHub under processen för enkel inloggning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till GitHub.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **GitHub**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **GitHub**.

    ![GitHub-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-github-test-user"></a>Skapa GitHub-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GitHub. GitHub stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](github-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på din GitHub-företagsplats som administratör.

2. Klicka på **Personer**.

    ![Personer](./media/github-tutorial/tutorial_github_config_github_08.png "Personer")

3. Klicka på **Invite member** (Bjud in medlem).

    ![Bjud in användare](./media/github-tutorial/tutorial_github_config_github_09.png "Bjud in användare")

4. På dialogrutan **Invite member** (Bjud in medlem) utför du följande steg:

    a. I textrutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    ![Bjud in personer](./media/github-tutorial/tutorial_github_config_github_10.png "Bjud in personer")

    b. Klicka på **Skicka inbjudan**.

    ![Bjud in personer](./media/github-tutorial/tutorial_github_config_github_11.png "Bjud in personer")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på GitHub-panelen i åtkomstpanelen bör du automatiskt loggas in på GitHub som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
