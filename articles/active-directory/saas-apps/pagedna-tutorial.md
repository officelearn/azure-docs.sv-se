---
title: 'Självstudier: Azure Active Directory-integrering med PageDNA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11cb309e6c49ed36247398909e5e1b7ad9f7bc42
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736434"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Självstudier: Azure Active Directory-integrering med PageDNA

I den här självstudien får du lära dig hur du integrerar PageDNA med Azure Active Directory (AD Azure).

Integrera PageDNA med Azure AD ger dig följande fördelar:

* Du kan styra vem som har åtkomst till PageDNA i Azure AD.
* Du kan aktivera användarna att logga in automatiskt till PageDNA (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med PageDNA, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En PageDNA prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera PageDNA med Azure AD.

PageDNA har stöd för följande funktioner:

* SP-initierad enkel inloggning (SSO).

* Etableringen av just-in-time-användare.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Lägg till PageDNA från Azure Marketplace

Om du vill konfigurera integrering av PageDNA i Azure AD måste du lägga till PageDNA från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure Portal](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program, Välj **+ nytt program** överst i fönstret.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **PageDNA**. I sökresultaten väljer **PageDNA**, och välj sedan **Lägg till** att lägga till programmet.

    ![PageDNA i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PageDNA baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i PageDNA.

Om du vill konfigurera och testa Azure AD enkel inloggning med PageDNA, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  vill tillåta att användarna använda den här funktionen.
1. **[Konfigurera PageDNA enkel inloggning](#configure-pagedna-single-sign-on)**  att konfigurera inställningar för enkel inloggning på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Skapa en testanvändare PageDNA](#create-a-pagedna-test-user)**  så att det finns en användare med namnet Britta Simon i PageDNA som är kopplad till Azure AD-användare med namnet Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med PageDNA, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **PageDNA** application integration markerar **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret gör följande:

    ![PageDNA domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    1. I den **inloggnings-URL** anger en URL med någon av följande mönster:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. I den **identifierare (entitets-ID)** anger en URL med någon av följande mönster:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. För att få dessa värden kan kontakta den [PageDNA supportteamet](mailto:success@pagedna.com). Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** fönstret i Azure-portalen.

1. I den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** att hämta **certifikat (Raw)** från de angivna alternativen och spara den på din dator.

    ![Alternativet certifikat (Raw) download](common/certificateraw.png)

1. I den **konfigurera PageDNA** avsnittet, kopiera den URL eller URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD Identifier**
   * **URL för utloggning**

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurera PageDNA enkel inloggning

För att konfigurera enkel inloggning på PageDNA sida, skickar det nedladdade certifikatet (Raw) och lämpliga kopierade URL: en från Azure portal för att den [PageDNA supportteamet](mailto:success@pagedna.com). PageDNA team gör att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen med namnet Britta Simon.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst PageDNA.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **PageDNA**.

    ![Fönstret för Enterprise-program](common/enterprise-applications.png)

1. I listan med program väljer **PageDNA**.

    ![PageDNA i programlistan](common/all-applications.png)

1. I den vänstra rutan under **hantera**väljer **användare och grupper**.

    ![Alternativet ”användare och grupper”](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**, och välj sedan **användare och grupper** i den **Lägg till tilldelning** fönstret.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i den **användare** och välj sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML-försäkran sedan i den **Välj roll** fönstret Välj rätt roll för användaren i listan. Längst ned i fönstret Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-pagedna-test-user"></a>Skapa en PageDNA testanvändare

En användare med namnet Britta Simon skapas nu i PageDNA. Du behöver inte göra något för att skapa den här användaren. PageDNA stöder etableringen av just-in-time-användare som är aktiverat som standard. Om en användare med namnet Britta Simon inte redan finns i PageDNA, skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du väljer **PageDNA** i portalen Mina appar du bör vara loggas in automatiskt PageDNA prenumerationen som du ställer in enkel inloggning. Läs mer om portalen Mina appar [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)