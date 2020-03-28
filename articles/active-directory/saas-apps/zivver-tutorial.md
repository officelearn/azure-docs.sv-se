---
title: 'Självstudiekurs: Azure Active Directory-integrering med ZIVVER | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ZIVVER.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 64cb7ea0-df6c-4963-84d8-6f435980e2de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 02b292c5db3d20f56d7b8291ea31d8da9863809b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233249"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Självstudiekurs: Azure Active Directory-integrering med ZIVVER

I den här självstudien får du lära dig hur du integrerar ZIVVER med Azure Active Directory (Azure AD).
Genom att integrera ZIVVER med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ZIVVER.
* Du kan aktivera dina användare automatiskt inloggad på ZIVVER (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ZIVVER behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* ZIVVER enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ZIVVER stöder **IDP** initierad SSO

## <a name="adding-zivver-from-the-gallery"></a>Lägga till ZIVVER från galleriet

Om du vill konfigurera integreringen av ZIVVER i Azure AD måste du lägga till ZIVVER från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till ZIVVER från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **ZIVVER**i sökrutan och välj **ZIVVER** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![ZIVVER i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med ZIVVER baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ZIVVER upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med ZIVVER måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ZIVVER Enkel inloggning](#configure-zivver-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ZIVVER-testanvändare](#create-zivver-test-user)** – om du vill ha en motsvarighet till Britta Simon i ZIVVER som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med ZIVVER:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **ZIVVER-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ZIVVER-domän och webbadresser med enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://app.zivver.com/SAML/Zivver`

5. ZIVVER-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. ZIVVER-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig ZIVVER-programmet att få fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Namnområde | Källattribut|
    | ---------------| --------------- |
    | ZivverAccountKey (olikartade) | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >Om du använder en hybridkonfiguration med Active Directory lokalt och Azure AD Connect Tool bör VALUE ställas in på`user.objectGUID`

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

7. Klicka på **Hämta** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** och klicka på **Kopiera** för att kopiera **url:en appfederationsmetadata** från de angivna alternativen enligt dina krav och spara den på datorn. **Federation Metadata XML**

    ![Hämtningslänken för certifikat-URL](./media/zivver-tutorial/metadataxmlurl.png)

8. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera ZIVVER** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zivver-single-sign-on"></a>Konfigurera enkel inloggning på ZIVVER

1. Logga in på ZIVVER-företagets [webbplats](https://app.zivver.com/login) som administratör i ett annat webbläsarfönster.

2. Klicka på ikonen **Organisationsinställningar** längst ned till vänster i webbläsarfönstret.

3. Gå till **Enkel inloggning**.

4. Öppna XML-filen federationsmetadata som du hämtade från Azure-portalen.

5. Klistra in **url-url:en för appfederationsmetadata** som du har sparat tidigare från Azure-portalen i textrutan **Url för identitetsprovidermetadata.**

6. Markera kryssrutan **Aktivera SSO**.

7. Klicka på **SPARA**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till ZIVVER.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **ZIVVER**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ZIVVER**i programlistan .

    ![Länken ZIVVER i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zivver-test-user"></a>Skapa ZIVVER-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i ZIVVER. Arbeta med [ZIVVER supportteam](https://support.zivver.com/) för att lägga till användarna i ZIVVER-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ZIVVER-panelen på åtkomstpanelen ska du automatiskt loggas in på den ZIVVER som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

