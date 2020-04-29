---
title: 'Självstudie: Azure Active Directory integrering med Fluxx Labs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102379"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Självstudie: Azure Active Directory integrering med Fluxx Labs

I den här självstudien får du lära dig att integrera Fluxx Labs med Azure Active Directory (Azure AD).
Genom att integrera Fluxx Labs med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Fluxx Labs.
* Du kan göra det möjligt för användarna att logga in automatiskt till Fluxx Labs (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Fluxx Labs behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Fluxx Labs enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Fluxx Labs stöder **IDP** INITIERAd SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Lägga till Fluxx Labs från galleriet

Om du vill konfigurera integreringen av Fluxx Labs i Azure AD måste du lägga till Fluxx Labs från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fluxx Labs från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Fluxx Labs**, väljer **Fluxx Labs** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Fluxx Labs i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Fluxx Labs baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Fluxx Labs.

Om du vill konfigurera och testa enkel inloggning med Fluxx Labs i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Fluxx Labs enkel inloggning](#configure-fluxx-labs-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Fluxx Labs test User](#create-fluxx-labs-test-user)** -om du vill ha en motsvarighet till Britta Simon i Fluxx Labs som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Fluxx Labs i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Fluxx Labs** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för Fluxx Labs-domän och URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io` |
    | För produktion | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | För produktion | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Fluxx Labs-klientens support team](mailto:travis@fluxxlabs.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Fluxx Labs** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurera Fluxx Labs enkel inloggning

1. Logga in på din Fluxx Labs-företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **administratör** under avsnittet **Inställningar** .

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. I panelen admin väljer du **plugin-program** > -**integreringar** och väljer sedan **SAML SSO – (inaktive rad)**

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. I avsnittet Attribute, utför följande steg:

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Markera kryss rutan **SAML SSO** .

    b. Skriv **/auth/SAML**i text rutan **begär ande Sök väg** .

    c. I text rutan **återanrops Sök väg** skriver du **/auth/SAML/callback**.

    d. I text rutan för **försäkrans konsument tjänst-URL (enkel inloggning)** anger du **SVARs-URL** -värdet, som du har angett i Azure Portal.

    e. I text rutan **mål grupp (SP entitets-ID)** anger du **ID** -värdet, som du har angett i Azure Portal.

    f. I text rutan för **URL för identitets leverantörens SSO-mål** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    g. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **identitets leverantörs certifikat** .

    h. I text rutan **namn på ID-format** anger `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`du värdet.

    i. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet har sparats kommer fältet att visas som tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Fluxx Labs.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Fluxx Labs**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Fluxx Labs**.

    ![Fluxx Labs-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-fluxx-labs-test-user"></a>Skapa Fluxx Labs test användare

För att Azure AD-användare ska kunna logga in på Fluxx Labs måste de tillhandahållas i Fluxx Labs. När det gäller Fluxx Labs är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Fluxx Labs företags webbplats som administratör.

2. Klicka på **ikonen**som visas nedan.

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. På instrument panelen klickar du på ikonen som visas nedan för att öppna kortet **nya personer** .

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. I avsnittet **nya personer** utför du följande steg:

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs använder e-post som unik identifierare för SSO-inloggningar. Fyll i fältet **SSO-UID** med användarens e-postadress som matchar e-postadressen, som de använder som inloggning med SSO.

    b. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Fluxx Labs på åtkomst panelen, bör du loggas in automatiskt på de Fluxx-labb som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

