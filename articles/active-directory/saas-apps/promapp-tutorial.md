---
title: 'Självstudier: Azure Active Directory-integrering med Promapp | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e91351d4571eaa084865c5a179ed05e6c773b952
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240416"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Självstudier: Azure Active Directory-integrering med Promapp

I de här självstudierna lär du dig att integrera Promapp med Azure Active Directory (AD Azure).
Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Promapp.
* Du kan aktivera användarna att logga in automatiskt till Promapp (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Promapp, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* En Promapp-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Promapp stöder SP-initierat och IdP-initierad SSO.

* Promapp stöder just-in-time-användaretablering.

## <a name="add-promapp-from-the-gallery"></a>Lägg till Promapp från galleriet

Om du vill konfigurera integrering av Promapp i Azure AD, som du behöver lägga till Promapp från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Promapp**. Välj **Promapp** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Promapp med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Promapp.

Om du vill konfigurera och testa Azure AD enkel inloggning med Promapp, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Promapp enkel inloggning](#configure-promapp-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Promapp genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/), på sidan Promapp application integration väljer **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan om du vill konfigurera programmet i IdP-initierad läge, utför följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I den **identifierare** anger en URL i det här mönstret:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Azure AD-integrering med Promapp konfigureras för närvarande endast för tjänstinitierade autentisering. (Det vill säga kommer att en Promapp URL initierar autentiseringsprocessen.) Men **svars-URL** fält är ett obligatoriskt fält.

    1. I den **svars-URL** anger en URL i det här mönstret:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser**. I den **inloggnings-URL** anger en URL i det här mönstret:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Promapp domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda det faktiska ID: t, svars-URL och inloggnings-URL. Kontakta den [Promapp supportteamet](https://www.promapp.com/about-us/contact-us/) att hämta värdena. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **certifikat (Base64)** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

7. I den **konfigurera Promapp** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav:

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-promapp-single-sign-on"></a>Konfigurera Promapp enkel inloggning

1. Logga in på webbplatsen Promapp företag som en administratör.

2. I menyn högst upp i fönstret Välj **Admin**:
   
    ![Välj Admin][12]

3. Välj **konfigurera**:
   
    ![Välj Konfigurera][13]

4. I den **Security** dialogrutan rutan, vidta följande steg.
   
    ![Dialogrutan säkerhet][14]
    
    1. Klistra in den **inloggnings-URL** som du kopierade från Azure-portalen till den **SSO-inloggnings-URL** box.
    
    1. I den **SSO - läge för enkel inloggning** väljer **valfritt**. Välj **Spara**.

       > [!NOTE]
       > Valfritt läge är endast för testning. När du är nöjd med konfigurationen väljer **krävs** i den **SSO - läge för enkel inloggning** listan om du vill tvinga alla användare att autentisera med Azure AD.

    1. I anteckningar, öppnar du det certifikat som du hämtade i föregående avsnitt. Kopiera innehållet i certifikatet utan den första raden ( **---BEGIN CERTIFICATE---** ) eller den sista raden ( **---END CERTIFICATE---** ). Klistra in certifikatet som innehåll till den **SSO-x.509-certifikat** och väljer sedan **spara**.

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Promapp.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Promapp**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Promapp**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="just-in-time-user-provisioning"></a>Etableringen av just-in-time-användare

Promapp stöder just-in-time-användaretablering. Den här funktionen är aktiverad som standard. Om en användare inte redan finns i Promapp, skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Promapp i åtkomstpanelen, bör det vara loggas in automatiskt till Promapp-instansen som du ställer in enkel inloggning. Läs mer om åtkomstpanelen [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
