---
title: 'Självstudiekurs: Azure Active Directory-integrering med OpsGenie | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095760"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Självstudiekurs: Azure Active Directory-integrering med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (Azure AD).
Genom att integrera OpsGenie med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till OpsGenie.
* Du kan aktivera dina användare så att de automatiskt loggas in på OpsGenie (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med OpsGenie behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* OpsGenie enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OpsGenie stöder **SP** initierade SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Lägga till OpsGenie från galleriet

Om du vill konfigurera integreringen av OpsGenie i Azure AD måste du lägga till OpsGenie från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till OpsGenie från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **OpsGenie**i sökrutan och välj **OpsGenie** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![OpsGenie i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med OpsGenie baserat på en testanvändare som heter **B. Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i OpsGenie upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med OpsGenie måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera OpsGenie Single Sign-On](#configure-opsgenie-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B. Simon kan använda azure AD enkel inloggning.
5. **[Skapa OpsGenie-testanvändare](#create-opsgenie-test-user)** – om du vill ha en motsvarighet till B. Simon i OpsGenie som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med OpsGenie:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **OpsGenie-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![OpsGenie Domän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.opsgenie.com/auth/login`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera OpsGenie.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-opsgenie-single-sign-on"></a>Konfigurera OpsGenie enkel inloggning

1. Öppna en annan webbläsarinstans och logga sedan in på OpsGenie som administratör.

2. Klicka på **Inställningar**och sedan på fliken **Enkel inloggning.**
   
    ![OpsGenie enkel inloggning](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Om du vill aktivera SSO väljer du **Aktiverad**.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Klicka på fliken **Azure Active Directory** i avsnittet **Provider.**
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Gör följande på dialogsidan i Azure Active Directory:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. I **sms-textrutan SAML 2.0** klistrar du in **url-värde**för inloggning som du har kopierat från Azure-portalen.
    
    b. I **metadataadressen:** textrutan klistrar du in **url-värdet för App Federation Metadata** som du har kopierat från Azure-portalen.
    
    c. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen som heter B. Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **B. Simon**.
  
    b. I fältet **Användarnamn** anger du **bsimon@yourcompanydomain.extension**  
    Till exempel, BSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera B. Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till OpsGenie.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **OpsGenie**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **OpsGenie**i programlistan .

    ![Länken OpsGenie i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Klicka på knappen **Välj** längst ned på skärmen **i** dialogrutan Användare **och grupper.**

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-opsgenie-test-user"></a>Skapa OpsGenie-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter B. Simon i OpsGenie. 

1. I ett webbläsarfönster loggar du in på din OpsGenie-klient som administratör.

2. Navigera till listan Användare genom att klicka på **Användare** på den vänstra panelen.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klicka på **Lägg till användare**.

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Skriv e-postadressen till B. Simon i Azure Active Directory i **textrutan e-post.**
   
    b. Skriv **B. Simon**i textrutan **Fullständigt namn** .
   
    c. Klicka på **Spara**. 

>[!NOTE]
>B. Simon får ett e-postmeddelande med instruktioner för hur du konfigurerar sin profil.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på OpsGenie-panelen på åtkomstpanelen ska du automatiskt loggas in på opsGenie som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

