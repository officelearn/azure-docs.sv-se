---
title: 'Självstudiekurs: Azure Active Directory-integrering med Rackspace SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rackspace SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093182"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Självstudiekurs: Azure Active Directory-integrering med Rackspace SSO

I den här självstudien får du lära dig hur du integrerar Rackspace SSO med Azure Active Directory (Azure AD).
Genom att integrera Rackspace SSO med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rackspace SSO.
* Du kan aktivera dina användare så att de automatiskt loggas in på Rackspace SSO (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Rackspace SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Rackspace SSO enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rackspace SSO stöder **SP** initierade SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Lägga till Rackspace SSO från galleriet

Om du vill konfigurera integreringen av Rackspace SSO i Azure AD måste du lägga till Rackspace SSO från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Rackspace SSO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Rackspace SSO**i sökrutan och välj **Rackspace SSO** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Rackspace SSO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Rackspace SSO baserat på en testanvändare som heter **Britta Simon**.
När du använder enkel inloggning med Rackspace skapas Rackspace-användarna automatiskt första gången de loggar in på Rackspace-portalen. 

Om du vill konfigurera och testa azure AD-enkel inloggning med Rackspace SSO måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Rackspace SSO Single Sign-On](#configure-rackspace-sso-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera attributmappning på Kontrollpanelen i Rackrymden](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – om du vill tilldela Rackspace-roller till Azure AD-användare.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Rackspace SSO:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Rackspace SSO-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp **metadatafilen för Tjänsteleverantören** som du kan hämta från [URL:en](https://login.rackspace.com/federate/sp.xml) och utför följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, de nödvändiga webbadresserna få auto fyllas automatiskt.

    d. Skriv en URL i textrutan **Sign-on-URL:**`https://login.rackspace.com/federate/`

    ![Rackspace SSO-domän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)   

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

Den här filen kommer att överföras till Rackspace för att fylla i nödvändiga konfigurationsinställningar för Identitetsfederation.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurera Rackspace SSO Enkel inloggning

Så här konfigurerar du enkel inloggning på **Rackspace SSO-sidan:**

1. Se dokumentationen på [Lägg till en identitetsprovider på Kontrollpanelen](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Det kommer att leda dig genom stegen för att:
    1. Skapa en ny identitetsprovider
    1. Ange en e-postdomän som användarna ska använda för att identifiera ditt företag när du loggar in.
    1. Ladda upp **XML-koden för federationsmetadata** som tidigare hämtats från Kontrollpanelen i Azure.

Detta konfigurerar korrekt de grundläggande SSO-inställningarna som behövs för Azure och Rackspace för att ansluta.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Rackspace SSO.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Rackspace SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Rackspace SSO**i programlistan .

    ![Rackspace SSO-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Konfigurera attributmappning på kontrollpanelen i Rackrymet

Rackspace använder en **princip för attributmappning** för att tilldela Rackspace-roller och grupper till dina enskilda inloggningsanvändare. **Princip för attributmappning** översätter Azure AD SAML-anspråk till de användarkonfigurationsfält som Rackspace kräver. Mer dokumentation finns i dokumentationen för Rackspace [Attribute Mapping Basics](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Några överväganden:

* Om du vill tilldela olika nivåer av Rackspace-åtkomst med Azure AD-grupper måste du aktivera gruppanspråket i Azure **Rackspace SSO** Single Sign-on-inställningarna. **Attributmappningsprincipen** används sedan för att matcha dessa grupper med önskade Rackspace-roller och grupper:

    ![Inställningarna för gruppanspråk](common/sso-groups-claim.png)

* Som standard skickar Azure AD UID för Azure AD-grupper i SAML-anspråket jämfört med namnet på gruppen. Men om du synkroniserar din lokala Active Directory till Azure AD har du möjlighet att skicka de faktiska namnen på grupperna:

    ![Inställningar för anspråksnamn för grupper](common/sso-groups-claims-names.png)

Följande exempel **Attributmappningsprincip** visar:
1. Ställa in Rackspace-användarens `user.name` namn på SAML-anspråket. Alla anspråk kan användas, men det är vanligast att ange detta till ett fält som innehåller användarens e-postadress.
1. Ange `admin` Rackspace-roller `billing:admin` och på en användare genom att matcha en Azure AD-grupp, med antingen gruppnamn eller grupp-UID. En *substitution* ersättning `"{0}"` av `roles` i fältet används och ersätts av `remote` resultatet av regeluttrycken.
1. Använda `"{D}"` *standardersättningen* för att låta Rackspace hämta ytterligare SAML-fält genom att leta efter standard- och välkända SAML-anspråk i SAML-utbytet.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Se till att du använder en textredigerare som validerar YAML-syntax när du redigerar principfilen.

Mer exempel finns i dokumentationen för Grunderna [i Rackspace-attributmappning.](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/)

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Rackspace SSO på åtkomstpanelen bör du automatiskt loggas in på den Rackspace SSO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också använda knappen **Validera** i **rackspace SSO-inställningarna** för enkel inloggning:

   ![Knappen SSO-validering](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

