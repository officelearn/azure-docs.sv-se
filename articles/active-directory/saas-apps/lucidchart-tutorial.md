---
title: 'Självstudie: Azure Active Directory integrering med Lucidchart | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc91d51d49ac1a317b88835e4d2e51773216020
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159507"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Självstudie: Azure Active Directory integrering med Lucidchart

I den här självstudien får du lära dig hur du integrerar Lucidchart med Azure Active Directory (Azure AD).
Genom att integrera Lucidchart med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Lucidchart.
* Du kan göra det möjligt för användarna att logga in automatiskt till Lucidchart (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Lucidchart behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Lucidchart-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lucidchart stöder **SP** -INITIERAd SSO
* Lucidchart stöder **just-in-Time** User-etablering

## <a name="adding-lucidchart-from-the-gallery"></a>Lägga till Lucidchart från galleriet

Om du vill konfigurera integreringen av Lucidchart i Azure AD måste du lägga till Lucidchart från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Lucidchart från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Lucidchart**, väljer **Lucidchart** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Lucidchart i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Lucidchart baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Lucidchart upprättas.

Om du vill konfigurera och testa enkel inloggning med Lucidchart i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Lucidchart-enkel inloggning](#configure-lucidchart-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Lucidchart test User](#create-lucidchart-test-user)** – om du vill ha en motsvarighet till Britta Simon i Lucidchart som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Lucidchart i Azure AD:

1. Välj **enkel inloggning**på sidan **Lucidchart** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Lucidchart-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL som: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Lucidchart** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lucidchart-single-sign-on"></a>Konfigurera Lucidchart enkel inloggning

1. Logga in på din Lucidchart-företags webbplats som administratör i ett annat webbläsarfönster.

2. I menyn högst upp klickar du på **team**.

    ![Dela](./media/lucidchart-tutorial/ic791190.png "Dela")

3. Klicka på **program \> hantera SAML**.

    ![Hantera SAML](./media/lucidchart-tutorial/ic791191.png "Hantera SAML")

4. Utför följande steg på dialog sidan **Inställningar för SAML-autentisering** :

    a. Välj **Aktivera SAML-autentisering**och klicka sedan på **valfri**.

    ![Inställningar för SAML-autentisering](./media/lucidchart-tutorial/ic791192.png "Inställningar för SAML-autentisering")

    b. Skriv din domän i text rutan **domän** och klicka sedan på **ändra certifikat**.

    ![Ändra certifikat](./media/lucidchart-tutorial/ic791193.png "Ändra certifikat")

    c. Öppna den hämtade metadatafilen, kopiera innehållet och klistra in den i text rutan **Ladda upp metadata** .

    ![Ladda upp metadata](./media/lucidchart-tutorial/ic791194.png "Ladda upp metadata")

    d. Välj **Lägg automatiskt till nya användare i teamet**och klicka sedan på **Spara ändringar**.

    ![Spara ändringar](./media/lucidchart-tutorial/ic791195.png "Spara ändringar")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Lucidchart.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Lucidchart**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Lucidchart**.

    ![Lucidchart-länken i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lucidchart-test-user"></a>Skapa Lucidchart test användare

Det finns inget åtgärds objekt som du kan använda för att konfigurera användar etablering till Lucidchart.  När en tilldelad användare försöker logga in på Lucidchart med hjälp av åtkomst panelen kontrollerar Lucidchart om användaren finns.  

Om det inte finns något användar konto tillgängligt ännu, skapas det automatiskt av Lucidchart.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Lucidchart på åtkomst panelen, bör du loggas in automatiskt på den Lucidchart som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
