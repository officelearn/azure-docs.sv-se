---
title: 'Självstudier: Azure Active Directory-integrering med Pega system | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och Pega system.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 195e7bf21fe1f6017705883f2ec692c182f15375
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560596"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudier: Azure Active Directory-integrering med Pega system

I de här självstudierna lär du dig att integrera Pega system med Azure Active Directory (AD Azure).

Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Pega system.
* Du kan aktivera användarna att vara automatiskt inloggad till Pega system (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har någon Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Pega system, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* En Pega system-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Pega system stöder SP-initierat och IdP-initierad SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Lägga till Pega system från galleriet

Om du vill konfigurera integreringen av Pega system till Azure AD, som du behöver lägga till Pega system från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Företagsprogram** > **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Pega system**. Välj **Pega system** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pega system med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Pega system.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pega system, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Pega system enkel inloggning](#configure-pega-systems-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare Pega system](#create-a-pega-systems-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Pega system genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Pega system** application integration markerar **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan om du vill konfigurera programmet i IdP-initierad läge, utför följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I den **identifierare** anger en URL i det här mönstret:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. I den **svars-URL** anger en URL i det här mönstret:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser** och utför följande steg.

    ![Pega system domän och URL: er med enkel inloggning för information](common/both-advanced-urls.png)

    1. I den **inloggnings-URL** anger tecknet på URL-värdet.

    1. I den **Vidarebefordransstatus** anger en URL i det här mönstret: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Värden som anges här platshållarna. Du måste använda det faktiska ID: t, svars-URL, logga in URL: en och URL: en för relay-tillstånd. Du kan hämta identifieraren och svars-URL-värden från ett Pega-program, som beskrivs senare i den här självstudien. För att få statusvärdet relay kan kontakta den [Pega system supportteam](https://www.pega.com/contact-us). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Programmet Pega system måste SAML-intyg kan på ett specifikt format. Om du vill ha dem i rätt format, som du behöver lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar de standardattribut. Välj den **redigera** ikonen för att öppna den **användarattribut** dialogrutan:

    ![Användarattribut](common/edit-attribute.png)

7. Förutom de attribut som visas i föregående skärmbild kräver Pega system-programmet några fler attribut som ska skickas tillbaka i SAML-svar. I den **användaranspråk** delen av den **användarattribut** dialogrutan rutan, utför följande steg för att lägga till dessa SAML-tokenattribut:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Dessa värden är specifika för din organisation. Ange lämpliga värden.

    1. Välj **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan:

    ![Välj Lägg till nytt anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    1. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    1. Lämna den **Namespace** tom.

    1. För den **källa**väljer **attributet**.

    1. I den **källattribut** väljer attribut-värde som visas för den raden.

    1. Välj **OK**.

    1. Välj **Spara**.

8. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **Federation Metadata-XML** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

9. I den **konfigurera Pega system** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav.

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurera Pega system enkel inloggning

1. Att konfigurera enkel inloggning på den **Pega system** sida, logga in på portalen Pega med ett administratörskonto i ett annat webbläsarfönster.

2. Välj **skapa** > **SysAdmin** > **autentiseringstjänst**:

    ![Välj autentiseringstjänst](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Utför följande steg på den **skapa autentiseringstjänst** skärmen.

    ![Skapa autentiseringstjänst skärm](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. I den **typ** väljer **SAML 2.0**.

    1. I den **namn** anger du ett namn (till exempel **Azure AD SSO**).

    1. I den **kort beskrivning** , ange en beskrivning.  

    1. Välj **skapa och öppna**.
    
4. I den **identitetsprovider (IdP) information** väljer **Import IdP metadata** och bläddra till metadatafilen som du laddade ned från Azure-portalen. Klicka på **skicka** att läsa in metadata:

    ![Identitetsavsnittet Provider (IdP) information](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Importen ska fylla i IdP-data som visas här:

    ![Importerade IdP-data](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Slutför följande stegen i den **Service Provider (SP) inställningar** avsnittet.

    ![Inställningar för service provider](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopiera den **enhetens identifiering** värde och klistra in den i den **identifierare** rutan den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    1. Kopiera den **Assertion konsument Service (ACS) plats** värde och klistra in den i den **svars-URL** rutan den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    1. Välj **inaktivera Begär signering**.

7. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, utför följande steg.

    ![Användardialogrutan](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** anger **brittasimon @\<företagsdomänen >.\< tillägget >**. (Till exempel BrittaSimon@contoso.com.)

    c. Välj **Show lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Pega system.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Pega system**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Pega system**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-pega-systems-test-user"></a>Skapa en testanvändare Pega system

Därefter måste du skapa en användare med namnet Britta Simon i Pega system. Arbeta med den [Pega system supportteam](https://www.pega.com/contact-us) att skapa användare.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Pega system i åtkomstpanelen, bör det vara loggas in automatiskt till Pega system-instans som du ställer in enkel inloggning. Mer information finns i [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)