---
title: 'Självstudier: Azure Active Directory-integrering med RingCentral | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997248"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Självstudier: Azure Active Directory-integrering med RingCentral

I den här självstudien får du lära dig hur du integrerar RingCentral med Azure Active Directory (AD Azure).
Integrera RingCentral med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till RingCentral.
* Du kan aktivera användarna att vara automatiskt inloggad till RingCentral (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med RingCentral, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* RingCentral enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för RingCentral **SP** -initierad SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Att lägga till RingCentral från galleriet

För att konfigurera integrering av RingCentral i Azure AD, som du behöver lägga till RingCentral från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till RingCentral från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **RingCentral**väljer **RingCentral** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![RingCentral i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med RingCentral baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i RingCentral upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med RingCentral, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RingCentral Single Sign-On](#configure-ringcentral-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa RingCentral testanvändare](#create-ringcentral-test-user)**  – du har en motsvarighet för Britta Simon i RingCentral som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med RingCentral:

1. I den [Azure-portalen](https://portal.azure.com/)på den **RingCentral** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts den **identifierare** och **svars-URL** värden får automatiskt ifylld i **SAML grundkonfiguration** avsnittet.

    ![RingCentral domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    I den **inloggnings-URL** text skriver en URL:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Du får den **tjänstleverantör metadatafil** på sidan RingCentral SSO-konfiguration som beskrivs senare i självstudien.

5. Om du inte har **tjänstleverantör metadatafil**, utför följande steg:

    a. I den **inloggnings-URL** textrutan anger du ett URL:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. I den **identifierare** textrutan anger du ett URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. I textrutan **Svars-URL** anger du en URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![RingCentral domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Konfigurera RingCentral Single Sign-On

1. I ett annat webbläsarfönster, loggar du in RingCentral som en administratör.

1. På överst, klickar du på **verktyg**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Gå till **enkel inloggning**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. På den **enkel inloggning** sidan under **SSO-konfiguration** avsnittet från **steg 1** klickar du på **redigera** och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. På den **Konfigurera enkel inloggning** utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klicka på **Bläddra** att ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. När du har överfört metadata hämta värdena fylls i **SSO allmän Information om** avsnittet.

    c. Under **attributmappning** väljer **kartan e-attributet till** som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klicka på **Spara**.

    e. Från **steg 2** klickar du på **hämta** att ladda ned den **tjänstleverantör metadatafil** och ladda upp den i **SAML grundkonfiguration** avsnittet att automatiskt fylla i den **identifierare** och **svars-URL** värdena i Azure-portalen.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. På samma sida, navigerar du till **aktivera SSO** avsnittet och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Välj **aktivera SSO-tjänsten**.

    * Välj **Tillåt användare att logga in med autentiseringsuppgifter för enkel inloggning eller RingCentral**.

    * Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RingCentral.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **RingCentral**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **RingCentral**.

    ![Länken RingCentral i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ringcentral-test-user"></a>Skapa RingCentral testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i RingCentral. Arbeta med [RingCentral klienten supportteamet](https://success.ringcentral.com/RCContactSupp) att lägga till användare i RingCentral-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen RingCentral i åtkomstpanelen, bör det vara loggas in automatiskt till RingCentral som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
