---
title: 'Självstudier: Azure Active Directory-integrering med Uberflip | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 69e86e486a9cdb058b972bda5176c14e15f4630a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682736"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Självstudier: Azure Active Directory-integrering med Uberflip

I den här självstudien får du lära dig hur du integrerar Uberflip med Azure Active Directory (AD Azure).

Integrera Uberflip med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Uberflip.
* Du kan aktivera användarna att logga in automatiskt till Uberflip (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Uberflip, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En Uberflip-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

Uberflip har stöd för följande funktioner:

* SP-initierat och IDP-initierad enkel inloggning (SSO).
* Etableringen av just-in-time-användare.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Lägg till Uberflip från Azure Marketplace

Om du vill konfigurera integrering av Uberflip i Azure AD måste du lägga till Uberflip från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **Azure Active Directory** i den vänstra rutan.

   ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

   ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program, Välj **+ nytt program** överst i fönstret.

   ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **Uberflip**. I sökresultaten väljer **Uberflip**, och välj sedan **Lägg till** att lägga till programmet.

   ![Uberflip i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Uberflip baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och en tillhörande användare i Uberflip.

Om du vill konfigurera och testa Azure AD enkel inloggning med Uberflip, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  vill tillåta att användarna använda den här funktionen.
1. **[Konfigurera Uberflip enkel inloggning](#configure-uberflip-single-sign-on)**  att konfigurera inställningar för enkel inloggning på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Skapa en testanvändare Uberflip](#create-an-uberflip-test-user)**  så att det finns en användare med namnet Britta Simon i Uberflip som är kopplad till Azure AD-användare med namnet Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Uberflip, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Uberflip** application integration markerar **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** fönstret, gör du något av följande, beroende på vilket läge för enkel inloggning som du vill konfigurera:

   * Konfigurera programmet i IDP-initierad SSO-läge i den **svars-URL (försäkran URL för Konsumenttjänst)** anger en URL med hjälp av följande mönster:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domän och URL: er med enkel inloggning för information](common/both-replyurl.png)

     > [!NOTE]
     > Det här värdet inte existerar. Uppdatera det här värdet med faktiska svars-URL. För att få det faktiska värdet kan kontakta den [Uberflip supportteamet](mailto:support@uberflip.com). Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** fönstret i Azure-portalen.

   * För att konfigurera programmet i SP-initierad SSO-läge, Välj **ange ytterligare webbadresser**, och i den **inloggnings-URL** anger du URL: en:

     `https://app.uberflip.com/users/login`

     ![Uberflip domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

1. På den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **Federation Metadata-XML**  från de angivna alternativen och spara den på din dator.

   ![Hämtningsalternativet Federation Metadata-XML](common/metadataxml.png)

1. I den **konfigurera Uberflip** fönstret Kopiera URL eller URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD Identifier**
   * **URL för utloggning**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurera Uberflip enkel inloggning

Du behöver skicka hämtade Federation Metadata XML-filen för att konfigurera enkel inloggning på Uberflip sida, och den aktuella kopieras URL: er från Azure portal för att den [Uberflip supportteamet](mailto:support@uberflip.com). Uberflip team gör att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen går du till den vänstra panelen och väljer **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och ”alla användare” alternativ](common/users.png)

1. Överst på skärmen väljer **+ ny användare**.

    ![Alternativet för nya användare](common/new-user.png)

1. I den **användaren** fönstret gör följande:

    ![Fönstret användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon\@\<företagsdomänen >.\< tillägget >**. Till exempel **BrittaSimon\@contoso.com**.

    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Uberflip.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **Uberflip**.

    ![Fönstret för Enterprise-program](common/enterprise-applications.png)

1. I listan med program väljer **Uberflip**.

    ![Uberflip i programlistan](common/all-applications.png)

1. I den vänstra rutan under **hantera**väljer **användare och grupper**.

    ![Alternativet ”användare och grupper”](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**, och välj sedan **användare och grupper** i den **Lägg till tilldelning** fönstret.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i den **användare** och välj sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML-försäkran sedan i den **Välj roll** fönstret Välj rätt roll för användaren i listan. Längst ned i fönstret Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-an-uberflip-test-user"></a>Skapa en Uberflip testanvändare

En användare med namnet Britta Simon skapas nu i Uberflip. Du behöver inte göra något för att skapa den här användaren. Uberflip stöder etableringen av just-in-time-användare som är aktiverat som standard. Om en användare med namnet Britta Simon inte redan finns i Uberflip, skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta den [Uberflip supportteamet](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du väljer **Uberflip** i portalen Mina appar du bör vara loggas in automatiskt Uberflip prenumerationen som du ställer in enkel inloggning. Läs mer om portalen Mina appar [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)