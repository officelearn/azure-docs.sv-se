---
title: 'Självstudier: Azure Active Directory-integrering med Kintone | Microsoft Docs'
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
ms.openlocfilehash: a66d1cb16f47ebe7aeba3685b3567620ae4a8a51
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564857"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Självstudier: Azure Active Directory-integrering med Kintone

I den här självstudien får du lära dig hur du integrerar Kintone med Azure Active Directory (AD Azure).
Integrera Kintone med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kintone.
* Du kan aktivera användarna att vara automatiskt inloggad för Kintone (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kintone, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kintone enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Kintone **SP** -initierad SSO

## <a name="adding-kintone-from-the-gallery"></a>Att lägga till Kintone från galleriet

För att konfigurera integrering av Kintone i Azure AD, som du behöver lägga till Kintone från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kintone från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Kintone**väljer **Kintone** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Kintone i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kintone baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Kintone upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kintone, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kintone Single Sign-On](#configure-kintone-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för Kintone](#create-kintone-test-user)**  – du har en motsvarighet för Britta Simon i Kintone som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Kintone:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Kintone** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Kintone domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.kintone.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Kintone klienten supportteamet](https://www.kintone.com/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Kintone** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kintone-single-sign-on"></a>Konfigurera Kintone Single Sign-On

1. I ett annat webbläsarfönster loggar du in din **Kintone** företagets plats som administratör.

1. Klicka på **inställningsikonen**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Klicka på **användare & systemadministration**.

    ![Användare och systemadministration](./media/kintone-tutorial/ic785880.png "användare & systemadministration")

1. Under **systemadministration \> Security** klickar du på **inloggning**.

    ![Logga in](./media/kintone-tutorial/ic785881.png "inloggning")

1. Klicka på **aktivera SAML-autentisering**.

    ![SAML-autentisering](./media/kintone-tutorial/ic785882.png "SAML-autentisering")

1. Utför följande steg i avsnittet SAML-autentisering:

    ![SAML-autentisering](./media/kintone-tutorial/ic785883.png "SAML-autentisering")

    a. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** att ladda upp din hämtade certifikatfil från Azure-portalen.

    d. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kintone.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Kintone**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Kintone**.

    ![Länken Kintone i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kintone-test-user"></a>Skapa Kintone testanvändare

Om du vill aktivera Azure AD-användare att logga in på Kintone, måste de etableras i Kintone. När det gäller Kintone är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **Kintone** företagets plats som administratör.

1. Klicka på **inställningsikonen**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Klicka på **användare & systemadministration**.

    ![Användaren & systemadministration](./media/kintone-tutorial/ic785880.png "användaren & systemadministration")

1. Under **Användaradministration**, klickar du på **avdelningar och användare**.

    ![Avdelning och användare](./media/kintone-tutorial/ic785888.png "avdelning och användare")

1. Klicka på **Ny användare**.

    ![Nya användare](./media/kintone-tutorial/ic785889.png "nya användare")

1. I den **ny användare** avsnittet, utför följande steg:

    ![Nya användare](./media/kintone-tutorial/ic785890.png "nya användare")

    a. Ange ett **visningsnamn**, **inloggningsnamn**, **nytt lösenord**, **Bekräfta lösenord**, **e-postadress**, och annan information om en giltig Azure AD-konto som du vill etablera till relaterade textrutor.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra Kintone användare konto verktyg för att skapa eller API: er som tillhandahålls av Kintone att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Kintone i åtkomstpanelen, bör det vara loggas in automatiskt för Kintone som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
