---
title: 'Självstudier: Azure Active Directory-integrering med Workspot kontroll | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning för Azure Active Directory och Workspot kontroll.
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
ms.openlocfilehash: 92173e760cf8207a15c643ca75913145ed343b55
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517754"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Självstudier: Azure Active Directory-integrering med Workspot kontroll

I den här självstudien får du lära dig hur du integrerar Workspot kontroll med Azure Active Directory (AD Azure). När du integrerar Workspot kontroll med Azure AD, kan du:

* Använda Azure AD för att kontrollera vem som har åtkomst till Workspot kontroll.
* Användarna kan logga in automatiskt till Workspot kontroll (enkel inloggning [SSO]) med hjälp av sina Azure AD-konton.
* Hantera konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Workspot kontroll, behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).

* En Workspot kontroll med enkel inloggning på aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

> [!Note]
> Workspot kontrollen stöder SP-initierat och IDP-initierad SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Lägger till Workspot kontroll från galleriet

Om du vill konfigurera integrering av Workspot kontroll i Azure AD måste du lägga till Workspot kontroll från galleriet i din lista över hanterade SaaS-appar.

**Följ dessa steg för att lägga till Workspot kontroll från galleriet:**

1. I den vänstra rutan i den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram** och välj **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

3. Välj **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Workspot kontroll**väljer **Workspot kontroll** i resultatrutan och välj sedan **Lägg till**.

     ![Fönstret ”Lägg till från galleriet”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workspot kontroll för Britta Simon en testanvändare.
För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren Workspot kontroll.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workspot kontroll, måste du utföra följande uppgifter:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda funktionen.
2. [Konfigurera Workspot kontroll enkel inloggning](#configure-workspot-control-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning för Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en testanvändare Workspot kontroll](#create-a-workspot-control-test-user) att upprätta en motsvarighet för Britta Simon Workspot kontrollen som är länkad till en Azure AD-representation av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg om du vill konfigurera Azure AD enkel inloggning med Workspot kontroll:

1. På den **Workspot kontroll** programsidan integrering i den [Azure-portalen](https://portal.azure.com/)väljer **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** väljer **SAML** läge för att aktivera enkel inloggning.

    ![Välj en enda inloggning väljer metod-fönster](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** (pennikonen) att komma åt **SAML grundkonfiguration**.

    ![Redigera ikon markerat ”grundläggande SAML-konfiguration”](common/edit-urls.png)

4. I den **SAML grundkonfiguration** om du vill konfigurera programmet i IDP-initierad läge, Följ dessa steg:

    ![Workspot kontroll domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    1. I den **identifierare** text, ange en URL i följande mönster:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. I den **svars-URL** text, ange en URL i följande mönster:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser**.

    ![Workspot kontroll domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I den **inloggnings-URL** text, ange en URL i följande mönster:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Dessa värden är inte verkliga. Ersätt värdena med det faktiska ID: t, svars-URL och inloggnings-URL. Kontakta den [Workspot kontroll klienten supportteamet](mailto:support@workspot.com) att hämta dessa värden. Eller du kan också referera till mönster i den **SAML grundkonfiguration** på Azure portal.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att hämta **certifikat (Base64)** bland de tillgängliga alternativen efter behov. Spara det på din dator.

    ![Länk för hämtning av certifikat (Base64)](common/certificatebase64.png)

7. I den **ställa in kontroller för Workspot** avsnittet, kopiera de lämpliga URL: er efter behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - **Inloggnings-URL**

    - **Azure AD Identifier**

    - **URL för utloggning**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurera Workspot kontroll enkel inloggning

1. I ett annat webbläsarfönster, loggar du in Workspot kontroll som en administratör.

2. I verktygsfältet högst upp på sidan Välj **installationsprogrammet** och sedan **SAML**.

    ![Installationsalternativ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. I den **Security Assertion Markup Language Configuration** fönstret gör så här:
 
    ![Fönster för Security Assertion Markup Language konfiguration](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. I den **entitets-ID** rutan, klistra in den **Azure Ad-identifierare** som du kopierade från Azure-portalen.

    1. I den **tjänst-URL för inloggning** rutan, klistra in den **inloggnings-URL** som du kopierade från Azure-portalen.

    1. I den **Utloggning** rutan, klistra in den **URL för utloggning** som du kopierade från Azure-portalen.

    1. Välj **uppdateringsfil** överföra till X.509-certifikatet Base64-kodade certifikatet som du hämtade från Azure-portalen.

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen.

1. I den vänstra rutan i Azure Portal, Välj **Azure Active Directory**, **användare**, och sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](common/users.png)

2. Välj **ny användare** överst i fönstret.

    ![”Ny användare” knappen](common/new-user.png)

3. Följ dessa steg i egenskaperna för användaren:

    ![Egenskapsfönstret för användare](common/user-properties.png)

    1. I den **namn** anger **BrittaSimon**.
  
    1. I den **användarnamn** fältet, anger du **brittasimon @* yourcompanydomain.extension***. Ange till exempel  **BrittaSimon@contoso.<i> </i>com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Antecknar du värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska ge du Britta Simon åtkomst till Workspot kontroll för att aktivera henne att använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram**, **alla program**, och sedan **Workspot kontroll**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

2. Från listan med program väljer **Workspot kontroll**.

    ![Länken Workspot kontroll i listan med program](common/all-applications.png)

3. På menyn på vänster sida väljer **användare och grupper**.

    ![Länken ”användare och grupper”](common/users-groups-blade.png)

4. Välj den **Lägg till användare** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** fönster.

    ![Fönstret ”Lägg till tilldelning”](common/add-assign-user.png)

5. I den **användare och grupper** väljer **Britta Simon** från den **användare** lista. Klicka sedan på **Välj**.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du rätt roll för användaren i listan i den **Välj roll** fönster. Klicka sedan på **Välj** längst ned på sidan.

7. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-workspot-control-test-user"></a>Skapa en testanvändare Workspot kontroll

Om du vill aktivera Azure AD-användare att logga in på Workspot kontroll, måste de etableras i Workspot kontroll. Etablering är en manuell aktivitet.

**Följ dessa steg om du vill konfigurera ett användarkonto:**

1. Logga in på Workspot kontroll som en administratör.

2. I verktygsfältet högst upp på sidan Välj **användare** och sedan **Lägg till användare**.

    ![”Användare”-alternativ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. I den **lägga till en ny användare** fönstret gör så här:

    ![Fönstret ”Lägg till en ny användare”](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. I **Förnamn** anger det första namnet för en användare, till exempel **Britta**.

    1. I **efternamn** text anger efternamn för användaren, till exempel **Simon**.

    1. I **e-post** anger e-postadressen för användaren, till exempel  **Brittasimon@contoso.<i> </i>com**.

    1. Välj en lämplig användarroll från den **rollen** listrutan.

    1. Välj önskad användargrupp från den **grupp** listrutan.

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska vi testa våra Azure AD enkel inloggning för konfiguration via *åtkomstpanelen*.

När du klickar på den **Workspot kontroll** panelen i åtkomstpanelen, det bör vara loggas in automatiskt till Workspot kontrollen som du ställer in enkel inloggning. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/en-us/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/tutorial-list)

- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
