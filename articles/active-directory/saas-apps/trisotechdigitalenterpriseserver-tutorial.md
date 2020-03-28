---
title: 'Självstudiekurs: Azure Active Directory-integrering med Trisotech Digital Enterprise Server | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trisotech Digital Enterprise Server.
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
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088263"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Självstudiekurs: Azure Active Directory-integrering med Trisotech Digital Enterprise Server

I den här självstudien får du lära dig hur du integrerar Trisotech Digital Enterprise Server med Azure Active Directory (Azure AD).
Genom att integrera Trisotech Digital Enterprise Server med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Trisotech Digital Enterprise Server.
* Du kan aktivera dina användare automatiskt inloggad på Trisotech Digital Enterprise Server (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Trisotech Digital Enterprise Server behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech Digital Enterprise Server enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Trisotech Digital Enterprise Server stöder **SP** initierade SSO

* Trisotech Digital Enterprise Server stöder just in time-användaretablering **Just In Time**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Lägga till Trisotech Digital Enterprise Server från galleriet

Om du vill konfigurera integreringen av Trisotech Digital Enterprise Server i Azure AD måste du lägga till Trisotech Digital Enterprise Server från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Trisotech Digital Enterprise Server från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Trisotech Digital Enterprise Server**i sökrutan och välj **Trisotech Digital Enterprise Server** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Trisotech Digital Enterprise Server i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Trisotech Digital Enterprise Server baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Trisotech Digital Enterprise Server upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Trisotech Digital Enterprise Server måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Trisotech Digital Enterprise Server Enkel inloggning](#configure-trisotech-digital-enterprise-server-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Trisotech Digital Enterprise Server testanvändare](#create-trisotech-digital-enterprise-server-test-user)** - att ha en motsvarighet till Britta Simon i Trisotech Digital Enterprise Server som är kopplad till Azure AD representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Trisotech Digital Enterprise Server:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Trisotech Digital Enterprise** **Server-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Trisotech Digital Enterprise Server Domain och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.trisotech.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Trisotech Digital Enterprise Server Client supportteam](mailto:support@trisotech.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Konfigurera Trisotech Digital Enterprise Server Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Trisotech Digital Enterprise Server Configuration-företagswebbplats som administratör.

2. Klicka på **menyikonen** och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Välj **Användarleverantör**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Gör följande i avsnittet **Användarproviderkonfigurationer:**

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Välj **Säkert kontrollmarkeringsspråk 2 (SAML 2)** i listrutan i **autentiseringsmetoden**.

    b. I textrutan **Metadata-URL** klistrar du in **url-värdet för appfederationens metadata,** som du har kopierat från Azure-portalen.

    c. I textrutan **Program-ID** anger du URL:en med följande mönster: `https://<companyname>.trisotech.com`.

    d. Klicka på **Spara**

    e. Ange domännamnet i textrutan **Tillåtna domäner (tomma betyder alla),** tilldelas licenser för användare som matchar tillåtna domäner

    f. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Trisotech Digital Enterprise Server.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Trisotech Digital Enterprise Server**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Trisotech Digital Enterprise Server**i programlistan .

    ![Länken Trisotech Digital Enterprise Server i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Skapa Testanvändare för Trisotech Digital Enterprise Server

I det här avsnittet skapas en användare som heter Britta Simon i Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Trisotech Digital Enterprise Server skapas en ny efter autentisering.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du Supportteamet för [Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Trisotech Digital Enterprise Server på åtkomstpanelen bör du automatiskt loggas in på den Trisotech Digital Enterprise Server som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

