---
title: 'Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerText skydda Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7507e5d23898fd069797c14d0fa18419b8345d78
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736301"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger

Lär dig hur du integrerar TigerText skydda Messenger med Azure Active Directory (AD Azure) i den här självstudien.

Integrera TigerText skydda Messenger med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TigerText skydda Messenger.
* Du kan aktivera användarna att logga in automatiskt TigerText skydda Messenger (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TigerText Secure Messenger, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En TigerText skydda Messenger-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera TigerText skydda Messenger med Azure AD.

TigerText skydda Messenger stöder SP-initierad enkel inloggning (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Lägg till TigerText säker Messenger från Azure Marketplace

För att konfigurera integrering av TigerText skydda Messenger i Azure AD, måste du lägga till TigerText skydda Messenger från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure Portal](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program, Välj **+ nytt program** överst i fönstret.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **TigerText skydda Messenger**. I sökresultaten väljer **TigerText skydda Messenger**, och välj sedan **Lägg till** att lägga till programmet.

    ![TigerText skydda Messenger i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i TigerText skydda Messenger.

Om du vill konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  vill tillåta att användarna använda den här funktionen.
1. **[Konfigurera TigerText skydda Messenger enkel inloggning](#configure-tigertext-secure-messenger-single-sign-on)**  att konfigurera inställningar för enkel inloggning på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Skapa en testanvändare TigerText skydda Messenger](#create-a-tigertext-secure-messenger-test-user)**  så att det finns en användare med namnet Britta Simon i TigerText skydda Messenger som är kopplad till Azure AD-användare med namnet Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med TigerText Secure Messenger, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **TigerText skydda Messenger** application integration markerar **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. På den **väljer du en metod för enkel inloggning** fönstret Välj **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret gör följande:

    ![TigerText skydda Messenger domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    1. I den **inloggnings-URL** anger en URL:

       `https://home.tigertext.com`

    1. I den **identifierare (entitets-ID)** skriver du en URL med hjälp av följande mönster:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Den **identifierare (entitets-ID)** värde inte existerar. Uppdatera det här värdet med det faktiska ID: t. För att få värdet kan kontakta den [TigerText skydda Messenger supportteamet](mailto:prosupport@tigertext.com). Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** fönstret i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **Federation Metadata-XML**  från de angivna alternativen och spara den på din dator.

    ![Hämtningsalternativet Federation Metadata-XML](common/metadataxml.png)

1. I den **konfigurera TigerText skydda Messenger** avsnittet, kopiera den URL eller URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD Identifier**
   * **URL för utloggning**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurera TigerText skydda Messenger enkel inloggning

Du behöver skicka hämtade Federation Metadata XML-filen för att konfigurera enkel inloggning på TigerText skydda Messenger-sida, och den aktuella kopieras URL: er från Azure portal för att den [TigerText skydda Messenger supportteamet](mailto:prosupport@tigertext.com). TigerText skydda Messenger-teamet gör att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen går du till den vänstra panelen och väljer **Azure Active Directory**   > **Användare** > **Alla användare**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst TigerText skydda Messenger.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **TigerText skydda Messenger**.

    ![Fönstret för Enterprise-program](common/enterprise-applications.png)

1. I listan med program väljer **TigerText skydda Messenger**.

    ![TigerText skydda Messenger i listan med program](common/all-applications.png)

1. I den vänstra rutan under **hantera**väljer **användare och grupper**.

    ![Alternativet ”användare och grupper”](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**, och välj sedan **användare och grupper** i den **Lägg till tilldelning** fönstret.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i den **användare** och välj sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML-försäkran sedan i den **Välj roll** fönstret Välj rätt roll för användaren i listan. Längst ned i fönstret Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Skapa en testanvändare TigerText skydda Messenger

I det här avsnittet skapar du en användare som kallas Britta Simon i TigerText skydda Messenger. Arbeta med den [TigerText skydda Messenger supportteamet](mailto:prosupport@tigertext.com) att lägga till Britta Simon som en användare i TigerText skydda Messenger. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du väljer **TigerText skydda Messenger** i portalen Mina appar du bör vara loggas in automatiskt TigerText skydda Messenger-prenumeration som du ställer in enkel inloggning. Läs mer om portalen Mina appar [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)