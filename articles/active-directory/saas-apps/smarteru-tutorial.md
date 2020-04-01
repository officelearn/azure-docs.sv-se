---
title: 'Självstudiekurs: Azure Active Directory-integrering med SmarterU | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 712e7bcf513592f97950902faff2f7754093b9fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76549299"
---
> [!NOTE]
> Processen för att integrera SmarterU med Azure Active Directory dokumenteras och underhålls också i [SmarterU-hjälpsystemet](https://help.smarteru.com/ID2053086).

# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Självstudiekurs: Azure Active Directory-integrering med SmarterU

I den här självstudien får du lära dig hur du integrerar SmarterU med Azure Active Directory (Azure AD).
Genom att integrera SmarterU med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SmarterU.
* Du kan aktivera dina användare automatiskt inloggade på SmarterU (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SmarterU behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SmartareU enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SmarterU stöder **IDP-initierad** SSO

## <a name="adding-smarteru-from-the-gallery"></a>Lägga till SmarterU från galleriet

Om du vill konfigurera integreringen av SmarterU i Azure AD måste du lägga till SmarterU från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SmarterU från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på **Knappen Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SmarterU**i sökrutan och välj **SmartareU** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![SmartareU i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med SmarterU baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SmarterU upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SmarterU måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SmarterU Enkel inloggning](#configure-smarteru-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SmarterU-testanvändare](#create-smarteru-test-user)** – om du vill ha en motsvarighet till Britta Simon i SmarterU som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SmarterU:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SmarterU-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SmarterU-domän och webbadresser med enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du URL:en: `https://www.smarteru.com/`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SmarterU.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-smarteru-single-sign-on"></a>Konfigurera Smarteru enkel inloggning

1. I ett annat webbläsarfönster loggar du in på smarteru-företagets webbplats som administratör.

1. Klicka på **Kontoinställningar**i verktygsfältet högst upp .

    ![Kontoinställningar](./media/smarteru-tutorial/accountsettings.png)

1. På sidan för kontokonfiguration utför du följande steg:

    ![Extern auktorisering](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Välj **Aktivera extern auktorisering**.
  
    b. Välj fliken **SmartareU** i avsnittet **Huvudinloggningskontroll.**
  
    c. Välj fliken **SmartareU** i avsnittet **Använda standardinloggning.**
  
    d. Välj **Aktivera SAML**.
  
    e. Kopiera innehållet i den nedladdade metadatafilen och klistra sedan in det i **textrutan IdP Metadata.**

    f. Välj ett **identifieraresattribut/anspråk**.
  
    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SmarterU.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SmarterU**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SmarterU**i programlistan .

    ![SmarterU-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-smarteru-test-user"></a>Skapa SmarterU-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på SmarterU måste de etableras i SmarterU. När det gäller SmarterU är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. logga in på din **SmarterU-klient.**

1. Gå till **Användare**.

1. Gör följande i användaravsnittet:

    ![Ny användare](./media/smarteru-tutorial/adduser.png)  

    a. Klicka på **+Användare**.

    b. Skriv de relaterade attributvärdena för Azure AD-användarkontot i följande textrutor: **Primär e-post,** **medarbetar-ID**, **Lösenord**, **Verifiera lösenord**, **Förnamn**, **Efternamn**.

    c. Klicka på **Aktiv**.

    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra smartare verktyg för att skapa användarkonton eller API:er som tillhandahålls av SmarterU för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på smarteru-panelen på åtkomstpanelen ska du automatiskt loggas in på smartareu som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
