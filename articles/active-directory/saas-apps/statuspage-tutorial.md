---
title: 'Självstudier: Azure Active Directory-integrering med StatusPage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 49f77da5843bc2438ea7f82475b4faf753b66f09
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565216"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Självstudier: Azure Active Directory-integrering med StatusPage

I den här självstudien får du lära dig hur du integrerar StatusPage med Azure Active Directory (AD Azure).
Integrera StatusPage med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till StatusPage.
* Du kan aktivera användarna att vara automatiskt inloggad till StatusPage (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med StatusPage, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* StatusPage enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för StatusPage **IDP** -initierad SSO

## <a name="adding-statuspage-from-the-gallery"></a>Att lägga till StatusPage från galleriet

För att konfigurera integrering av StatusPage i Azure AD, som du behöver lägga till StatusPage från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till StatusPage från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **StatusPage**väljer **StatusPage** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![StatusPage i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med StatusPage baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i StatusPage upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med StatusPage, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera StatusPage Single Sign-On](#configure-statuspage-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare StatusPage](#create-statuspage-test-user)**  – du har en motsvarighet för Britta Simon i StatusPage som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med StatusPage:

1. I den [Azure-portalen](https://portal.azure.com/)på den **StatusPage** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![StatusPage domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Kontakta supporten StatusPage på [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)att begära metadata som behövs för att konfigurera enkel inloggning. 
    >
    > a. Kopiera utfärdarvärdet från metadata, och klistra in den i den **identifierare** textrutan.
    >
    > b. Från metadata, kopiera svars-URL och klistra in den i den **svars-URL** textrutan.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera StatusPage** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-statuspage-single-sign-on"></a>Konfigurera StatusPage Single Sign-On

1. I ett nytt webbläsarfönster loggar du in din StatusPage företagets webbplats som administratör.

1. I verktygsfältet klickar du på **Hantera konto**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på den **enkel inloggning** fliken.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. På konfigurationssidan för enkel inloggning utför du följande steg:

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. I den **mål-URL för enkel inloggning** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    c. Klicka på **Spara konfiguration**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till StatusPage.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **StatusPage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **StatusPage**.

    ![Länken StatusPage i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-statuspage-test-user"></a>Skapa StatusPage testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i StatusPage.

StatusPage har stöd för just-in-time-etablering. Du har redan har aktiverat det i [konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on).

**Om du vill skapa en användare som kallas Britta Simon i StatusPage, utför du följande steg:**

1. Inloggning till webbplatsen StatusPage företag som administratör.

1. Klicka på menyn längst upp **Hantera konto**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på den **teammedlemmar** fliken.
  
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicka på **Lägg till GRUPPMEDLEM**.
  
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Skriv den **e-postadress**, **Förnamn**, och **efternamn** av en giltig användare som du vill etablera till relaterade textrutor. 

    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Som **rollen**, Välj **Klientadministratör**.

1. Klicka på **skapa konto**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen StatusPage i åtkomstpanelen, bör det vara loggas in automatiskt till StatusPage som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
