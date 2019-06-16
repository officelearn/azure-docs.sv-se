---
title: 'Självstudier: Azure Active Directory-integrering med Percolate | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094603"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Självstudier: Azure Active Directory-integrering med Percolate

I de här självstudierna lär du dig att integrera Percolate med Azure Active Directory (AD Azure).

Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Percolate.
* Du kan aktivera användarna att logga in automatiskt till Percolate (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har någon Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Percolate, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Percolate-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Percolate stöder SP-initierat och IdP-initierad SSO.

## <a name="add-percolate-from-the-gallery"></a>Lägg till Percolate från galleriet

För att konfigurera integrering av Percolate i Azure AD, som du behöver lägga till Percolate från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Percolate**. Välj **Percolate** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Percolate med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Percolate.

Om du vill konfigurera och testa Azure AD enkel inloggning med Percolate, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Percolate enkel inloggning](#configure-percolate-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare Percolate](#create-a-percolate-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Percolate genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Percolate** application integration markerar **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan du behöver inte vidta några åtgärder för att konfigurera programmet i IdP-initierad läge. Appen är redan integrerat med Azure.

    ![Percolate domän och URL: er enkel inloggning för information](common/preintegrated.png)

5. Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser** och i den **inloggnings-URL** anger **https://percolate.com/app/login** :

   ![Percolate domän och URL: er enkel inloggning för information](common/metadata-upload-additional-signon.png)
6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **kopia** ikon för att kopiera den **Appfederationsmetadata** . Spara den här URL: en.

    ![Kopiera Appfederationsmetadata](common/copy-metadataurl.png)

7. I den **konfigurera Percolate** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav.

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurera Percolate enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in Percolate som en administratör.

2. Till vänster på sidan Välj **inställningar**:
    
    ![Välj inställningar](./media/percolate-tutorial/configure01.png)

3. I den vänstra rutan väljer **SSO** under **organisation**:

    ![Välj enkel inloggning under organisation](./media/percolate-tutorial/configure02.png)

    1. I den **inloggnings-URL** rutan, klistra in den **inloggnings-URL** värde som du kopierade från Azure-portalen.

    1. I den **entitets-ID** rutan, klistra in den **Azure AD-identifierare** värde som du kopierade från Azure-portalen.

    1. Öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen i anteckningar. Kopiera innehållet och klistra in den i den **x509 certifikat** box.

    1. I den **e-attributet** anger **e-postadress**.

    1. Den **metadata-URL för identitetsprovider** rutan är ett valfritt fält. Om du har kopierat en **Appfederationsmetadata** från Azure-portalen, du kan klistra in den i den här rutan.

    1. I den **bör AuthNRequests signeras?** väljer **nr**.

    1. I den **aktivera SSO Automatisk etablering** väljer **nr**.

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >** . (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure AD enkel inloggning ger användarens företagsidentitet åtkomst Percolate.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Percolate**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Percolate**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Användare och grupper](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-percolate-test-user"></a>Skapa en Percolate testanvändare

Om du vill aktivera Azure AD-användare att logga in på Percolate som du behöver lägga till dem i Percolate. Du måste lägga till dem manuellt.

Skapa ett användarkonto genom att göra följande:

1. Logga in på Percolate som en administratör.

2. I den vänstra rutan väljer **användare** under **organisation**. Välj **nya användare**:

    ![Välj nya användare](./media/percolate-tutorial/configure03.png)

3. På den **skapa användare** utför följande steg.

    ![Skapa användarsidan](./media/percolate-tutorial/configure04.png)

    1. I den **e-post** anger du e-postadressen för användaren. Till exempel brittasimon@contoso.com.

    1. I den **fullständigt namn** anger du namnet på användaren. Till exempel **Brittasimon**.

    1. Välj **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Percolate i åtkomstpanelen, bör det vara loggas in automatiskt till Percolate-instansen som du ställer in enkel inloggning. Mer information finns i [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)