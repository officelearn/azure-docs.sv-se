---
title: 'Självstudiekurs: Azure Active Directory-integrering med Pega Systems | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pega Systems.
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
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026802"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudiekurs: Azure Active Directory-integrering med Pega Systems

I den här självstudien får du lära dig hur du integrerar Pega-system med Azure Active Directory (Azure AD).

Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Pega Systems.
* Du kan aktivera dina användare så att de automatiskt loggas in på Pega Systems (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Pega Systems måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [månads utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En Pega Systems-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* Pega Systems stöder SP-initierad och IdP-initierad SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Lägg till Pega Systems från galleriet

Om du vill konfigurera integreringen av Pega Systems i Azure AD måste du lägga till Pega Systems från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Ange **Pega Systems**i sökrutan . Välj **Pega Systems** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Pega Systems med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Pega Systems.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Pega Systems måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Pega Systems enkel inloggning på](#configure-pega-systems-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en Pega Systems-testanvändare](#create-a-pega-systems-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Pega Systems:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Pega** **Systems-programintegration:**

    ![Välj Enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande steg om du vill konfigurera programmet i IdP-initierat läge i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. Ange en URL i det här mönstret i rutan **Identifierare:**

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Ange en URL i det här mönstret i rutan **Svara url:**

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare webbadresser** och utför följande steg.

    ![Pega Systems domän och webbadresser enkel inloggningsinformation](common/both-advanced-urls.png)

    1. Ange värdet för inloggningsadressen i rutan **Logga in på WEBBADRESS.**

    1. Ange en URL i det här mönstret i rutan **Återbelänad tillstånd:**`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De värden som anges här är platshållare. Du måste använda url:en för faktisk identifierare, svar, logga in på URL och relätillstånds-URL. Du kan hämta url-värden för identifierare och svara från ett Pega-program, vilket förklaras senare i den här självstudien. Kontakta [Pega Systems supportteam](https://www.pega.com/contact-us)för att få värdet för relätillstånd. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Pega Systems-programmet behöver SAML-påståendena för att vara i ett visst format. För att få dem i rätt format måste du lägga till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar standardattributen. Välj ikonen **Redigera** för att öppna dialogrutan **Användarattribut:**

    ![Användarattribut](common/edit-attribute.png)

7. Förutom de attribut som visas i föregående skärmdump kräver Pega Systems-programmet några fler attribut som ska skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** gör du följande steg för att lägga till dessa SAML-tokenattribut:

    
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

    1. Välj **Lägg till ett nytt anspråk** om du vill öppna dialogrutan Hantera **användarens anspråk:**

    ![Välj Lägg till nytt anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    1. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    1. Lämna **rutan Namnområde** tom.

    1. För **källan**väljer du **Attribut**.

    1. I attributlistan **Källa** väljer du det attributvärde som visas för den raden.

    1. Välj **Ok**.

    1. Välj **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **FEDERATION Metadata XML**enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

9. I avsnittet **Konfigurera Pega Systems** kopierar du lämpliga webbadresser baserat på dina behov.

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurera Pega Systems enkel inloggning

1. Om du vill konfigurera enkel inloggning på **Pega Systems-sidan** loggar du in på Pega-portalen med ett administratörskonto i ett annat webbläsarfönster.

2. Välj **Skapa** > **autentiseringstjänst för****SysAdmin:** > 

    ![Välj autentiseringstjänst](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Slutför följande steg på skärmen **Skapa autentiseringstjänst.**

    ![Skärmen Skapa autentiseringstjänst](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Välj **SAML 2.0**i listan **Typ** .

    1. Ange **Name** vilket namn som helst (till exempel **Azure AD SSO**).

    1. Ange en beskrivning i rutan **Kort beskrivning.**  

    1. Välj **Skapa och öppna**.
    
4. I informationsavsnittet **Identitetsprovider (IdP)** väljer du **Importera IdP-metadata** och bläddrar till metadatafilen som du hämtade från Azure-portalen. Klicka på **Skicka** om du vill läsa in metadata:

    ![Informationsavsnitt för IdP(Identity Provider)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Importen fyller i IdP-data som visas här:

    ![Importerade IdP-data](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Gör följande i avsnittet **Service Provider (SP).**

    ![Inställningar för tjänsteleverantörer](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopiera värdet **för entitetsidentifiering** och klistra in det i rutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. Kopiera **platsvärdet för kontroll konsumenttjänsten (ACS)** och klistra in det i rutan **Svara URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. Välj **Inaktivera signering av begäran**.

7. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan, väljer **Användare**och väljer sedan **Alla användare:**

    ![Välj alla användare](common/users.png)

2. Välj **Ny användare** högst upp på skärmen:

    ![Välj Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du **brittasimon@\<ditt företag>.\< förlängning>**. (Till exempel BrittaSimon@contoso.com.)

    c. Välj **Visa lösenord**och skriv sedan ned värdet i rutan **Lösenord.**

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till Pega Systems.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **Pega Systems**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Pega Systems**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-pega-systems-test-user"></a>Skapa en Pega Systems-testanvändare

Därefter måste du skapa en användare som heter Britta Simon i Pega Systems. Arbeta med [Pega Systems supportteam](https://www.pega.com/contact-us) för att skapa användare.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen Pega Systems på åtkomstpanelen bör du automatiskt loggas in i den Pega Systems-instans som du ställer in SSO för. Mer information finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)