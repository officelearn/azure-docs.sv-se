---
title: 'Självstudiekurs: Azure Active Directory-integrering med Workspot Control | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning för Azure Active Directory och Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086685"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Självstudiekurs: Azure Active Directory-integrering med Workspot Control

I den här självstudien får du lära dig hur du integrerar Workspot Control med Azure Active Directory (Azure AD). När du integrerar Workspot Control med Azure AD kan du:

* Använd Azure AD för att styra vem som har åtkomst till Workspot Control.
* Gör det möjligt för användare att automatiskt logga in på Workspot Control (enkel inloggning [SSO]) med hjälp av sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns [i Enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Workspot Control behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* En prenumeration med arbetsspotkontroll.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

> [!Note]
> Workspot Control stöder SP-initierad och IDP-initierad SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Lägga till Workspot Control från galleriet

Om du vill konfigurera integreringen av Workspot Control i Azure AD måste du lägga till Workspot Control från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Workspot Control från galleriet:**

1. Välj **Azure Active Directory**i den vänstra rutan i [Azure-portalen](https://portal.azure.com).

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

3. Välj **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Workspot Control,** väljer **Workspot Control** på resultatpanelen och väljer sedan **Lägg till**.

     ![Fönstret "Lägg till från galleriet"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Workspot Control för en testanvändare, Britta Simon.
För enkel inloggning för att fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i Workspot Control.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Workspot Control måste du utföra följande uppgifter:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda funktionen.
2. [Konfigurera enkel inloggning för Workspot Control](#configure-workspot-control-single-sign-on) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning för Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Workspot Control-testanvändare](#create-a-workspot-control-test-user) för att skapa en motsvarighet till Britta Simon i Workspot Control som är länkad till Azure AD-representationen för användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Workspot Control:

1. På sidan Integrering av Programintegrering av **Workspot Control** i [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML-läge** för att aktivera enkel inloggning.

    ![Välj ett enda tecken på valmetodfönster](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** (penna) för att komma åt **grundläggande SAML-konfiguration**. **Set up Single Sign-On with SAML**

    ![Redigera ikonen markerad i "Grundläggande SAML-konfiguration"](common/edit-urls.png)

4. Gör så här om du vill konfigurera programmet i IDP-initierat läge i avsnittet **Grundläggande SAML-konfiguration:**

    ![Arbetsområdeskontrolldomän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    1. Ange en URL i följande mönster i textrutan **identifierare:**<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Ange en URL i följande mönster i textrutan **svars-URL:**<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare webbadresser**.

    ![Arbetsområdeskontrolldomän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Ange en URL i följande mönster i textrutan **Sign-on-URL:**<br/>
    ***https://<<i> </i>INSTANSNAMN>-saml.workspot.com/***

    > [!NOTE]
    > Dessa värden är inte verkliga. Ersätt dessa värden med den faktiska identifieraren, svars-URL:en och inloggnings-URL:en. Kontakta [supportteamet](mailto:support@workspot.com) för Workspot Control Client för att få dessa värden. Du kan också referera till mönstren i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Hämta** för att hämta certifikat (Base64) i avsnittet **SAML-signeringscertifikat** **(Base64)** från de tillgängliga alternativen enligt dina krav. Spara den på datorn.

    ![Nedladdningslänken för certifikat (Base64)](common/certificatebase64.png)

7. I avsnittet **Konfigurera Workspot Control** kopierar du lämpliga webbadresser enligt dina krav:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - **Inloggnings-URL**

    - **Azure AD-identifierare**

    - **Utloggnings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurera enkel inloggning för Arbetsspotkontroll

1. I ett annat webbläsarfönster loggar du in på Workspot Control som säkerhetsadministratör.

2. I verktygsfältet högst upp på sidan väljer du **Installationsprogrammet** och sedan **SAML**.

    ![Installationsalternativ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Gör så här i fönstret Konfiguration av **säkerhetspåslagsmarkering:**
 
    ![Fönstret För markeringsspråk för säkerhetspåstående](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. I rutan **Entitets-ID** klistrar du in **Azure Ad Identifier** som du kopierade från Azure-portalen.

    1. I rutan **Url för Inloggningstjänst** klistrar du in **inloggningsadressen** som du kopierade från Azure-portalen.

    1. I **rutan URL för utloggningstjänst** klistrar du in **url:en för utloggning** som du kopierade från Azure-portalen.

    1. Välj **Uppdatera fil** om du vill överföra det bas-64-kodade certifikatet som du hämtade från Azure-portalen till X.509-certifikatet.

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen.

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**, **Användare**och sedan **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp i fönstret.

    ![Knappen "Ny användare"](common/new-user.png)

3. Gör så här i egenskaperna för användaren:

    ![Fönstret Användaregenskaper](common/user-properties.png)

    1. Ange **BrittaSimon**i fältet **Namn** .
  
    1. I fältet **Användarnamn** anger du **brittasimon@* dittföretag.extension***. Ange till exempel ** BrittaSimon@contoso.<i> </i> com**.

    1. Markera kryssrutan **Visa lösenord.** Skriv sedan ned värdet som visas i rutan **Lösenord.**

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ger du Britta Simon åtkomst till Workspot Control så att hon kan använda Azure single sign-on.

1. I Azure-portalen väljer du **Enterprise Applications**, **Alla program**och sedan **Workspot Control**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

2. Välj **Workspot Control**i programlistan .

    ![Länken Workspot Control i programlistan](common/all-applications.png)

3. Välj **Användare och grupper**på menyn till vänster .

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare.** Välj sedan **Användare och grupper** i fönstret Lägg till **tilldelning.**

    ![Fönstret "Lägg till tilldelning"](common/add-assign-user.png)

5. Välj **Britta Simon** i listan **Användare** **och grupper.** Klicka sedan på **Välj**.

6. Om du förväntar dig något rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i fönstret **Välj roll.** Klicka sedan på **Markera** längst ned.

7. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-workspot-control-test-user"></a>Skapa en Workspot Control-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Workspot Control måste de etableras i Workspot Control. Etablering är en manuell aktivitet.

**Så här etablerar du ett användarkonto:**

1. Logga in på Workspot Control som säkerhetsadministratör.

2. I verktygsfältet högst upp på sidan väljer du **Användare** och **sedan Till användare**.

    !["Användare" alternativ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Gör så här i fönstret **Lägg till en ny användare:**

    ![Fönstret "Lägg till en ny användare"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. I rutan **Förnamn** anger du förnamnet på en användare, till exempel **Britta**.

    1. I textrutan **Efternamn** anger du användarens efternamn, till exempel **Simon**.

    1. I rutan **E-post** anger du användarens e-postadress, till exempel ** Brittasimon@contoso.<i> </i> com**.

    1. Välj lämplig användarroll i listrutan **Roll.**

    1. Välj lämplig användargrupp i listrutan **Grupp.**

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar vi vår Azure AD-konfiguration med enkel inloggning via *Åtkomstpanelen*.

När du klickar på panelen **Arbetskontroll** på Åtkomstpanelen bör du automatiskt loggas in på den Workspot Control som du konfigurerar SSO för. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
