---
title: 'Självstudier: Azure Active Directory-integrering med Trisotech digitala Enterprise Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trisotech digitala Enterprise-Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c5d1a791de5c295b4644e5633983a5369200498e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256591"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Självstudier: Azure Active Directory-integrering med Trisotech digitala Enterprise Server

I den här självstudien får du lära dig hur du integrerar Trisotech digitala Enterprise Server med Azure Active Directory (AD Azure).
Integrera Trisotech digitala Enterprise Server med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Trisotech digitala Enterprise-Server.
* Du kan aktivera användarna att vara automatiskt inloggad till Trisotech digitala Enterprise Server (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Trisotech digitala Enterprise Server behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech digitala Enterprise Server enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Trisotech digitala Enterprise Server stöder **SP** -initierad SSO

* Trisotech digitala Enterprise Server stöder **Just In Time** etableringen av användare

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Att lägga till Trisotech digitala Enterprise Server från galleriet

För att konfigurera integrering av Trisotech digitala Enterprise Server i Azure AD, som du behöver lägga till Trisotech digitala Enterprise Server från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Trisotech digitala Enterprise Server från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Trisotech digitala Enterprise Server**väljer **Trisotech digitala Enterprise Server** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

     ![Trisotech digitala Enterprise-Server i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Trisotech digitala Enterprise Server ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Trisotech digitala Enterprise Server enkel inloggning](#configure-trisotech-digital-enterprise-server-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Trisotech digitala Enterprise Server testanvändare](#create-trisotech-digital-enterprise-server-test-user)**  – du har en motsvarighet för Britta Simon i Trisotech digitala företagsservern som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Trisotech digitala Enterprise Server:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Trisotech digitala Enterprise Server** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Trisotech digitala Enterprise Server-domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://<companyname>.trisotech.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Trisotech digitala Enterprise Server Client supportteamet](mailto:support@trisotech.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Konfigurera Trisotech Digital Enterprise Server enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din Trisotech digitala Enterprise-serverkonfiguration företagets webbplats som administratör.

2. Klicka på den **menyikonen** och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Välj **Användarprovider**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. I den **providern användarkonfigurationer** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Välj **skyddas Assertion Markup Language 2.2 SAML** i listrutan i det **autentiseringsmetod**.

    b. I den **Metadata_url** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat utgör Azure-portalen.

    c. I den **program-ID** textrutan anger du URL: en med hjälp av följande mönster: `https://<companyname>.trisotech.com`.

    d. Klicka på **Spara**

    e. Ange domännamnet i den **tillåtna domäner (empty innebär alla)** textrutan automatiskt tilldelar licenser för användare som matchar de domäner som tillåts

    f. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Trisotech digitala Enterprise-Server.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Trisotech digitala Enterprise Server**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Trisotech digitala Enterprise Server**.

    ![Länken Trisotech digitala Enterprise-Server i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Skapa testanvändare Trisotech digitala Enterprise Server

I det här avsnittet skapas en användare som kallas Britta Simon i Trisotech digitala Enterprise-Server. Trisotech digitala Enterprise Server stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Trisotech digitala Enterprise Server, skapas en ny efter autentisering.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Trisotech digitala Enterprise Server-supportteamet](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Trisotech digitala Enterprise Server i åtkomstpanelen bör det vara loggas in automatiskt till företagsservern Trisotech digitala som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

