---
title: 'Självstudier: Azure Active Directory-integrering med Rackspace SSO | Microsoft Docs'
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
ms.openlocfilehash: fd420ea3fc4faae7fe4510a72204d71acaa3549a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009952"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Självstudier: Azure Active Directory-integrering med Rackspace SSO

I den här självstudien får du lära dig hur du integrerar Rackspace SSO med Azure Active Directory (AD Azure).
Integrerar Rackspace SSO med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rackspace SSO.
* Du kan aktivera användarna att vara automatiskt inloggad till Rackspace SSO (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Rackspace SSO, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Rackspace SSO enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Rackspace SSO **SP** -initierad SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Att lägga till Rackspace SSO från galleriet

Om du vill konfigurera integreringen av Rackspace SSO till Azure AD, som du behöver lägga till Rackspace SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rackspace SSO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Rackspace SSO**väljer **Rackspace SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Rackspace SSO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Rackspace SSO baserat på en testanvändare kallas **Britta Simon**.
När du använder enkel inloggning med Rackspace, skapas Rackspace användare automatiskt första gången de loggar in på portalen Rackspace. 

Om du vill konfigurera och testa Azure AD enkel inloggning med Rackspace SSO, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Rackspace-SSO](#configure-rackspace-sso-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera attributmappning i Kontrollpanelen Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)**  – om du vill tilldela Rackspace roller till Azure AD-användare.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Rackspace SSO:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Rackspace SSO** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** avsnittet, ladda upp den **tjänstleverantör metadatafil** som du kan hämta från den [URL](https://login.rackspace.com/federate/sp.xml) och utför följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har har överförts, hämta URL: er som behövs fylls i automatiskt automatiskt.

    d. I rutan **Inloggnings-URL** anger du en URL: `https://login.rackspace.com/federate/`

    ![Rackspace SSO domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)   

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

Den här filen ska överföras till Rackspace att fylla i nödvändiga konfigurationsinställningar för identitetsfederation.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurera enkel inloggning Rackspace enkel inloggning

Att konfigurera enkel inloggning på **Rackspace SSO** sida:

1. Finns i dokumentationen på [lägga till en identitetsprovider i Kontrollpanelen](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Det leder dig igenom stegen för att:
    1. Skapa en ny identitetsprovider
    1. Ange en e-postdomän som användarna ska använda för att identifiera ditt företag när du loggar in.
    1. Ladda upp den **XML-Metadata för Federation** tidigare hämtade från Azure-Kontrollpanelen.

Detta konfigureras korrekt de grundläggande SSO-inställningar som krävs för Azure och Rackspace för att ansluta.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rackspace SSO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Rackspace SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Rackspace SSO**.

    ![Länken Rackspace SSO i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Konfigurera attributmappning Rackspace på Kontrollpanelen

Rackspace använder en **attributet mappning princip** tilldela Rackspace roller och grupper till dina användare för enkel inloggning. Den **attributet mappning princip** översätter Azure AD SAML anspråk till användaren konfigurationsfält Rackspace kräver. Mer dokumentation finns i Rackspace [attributet mappning grunderna dokumentation](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Vissa förutsättningar:

* Om du vill tilldela olika behörighetsnivåer Rackspace åtkomst med hjälp av Azure AD-grupper, behöver du vill aktivera anspråket grupper i Azure **Rackspace SSO** inställningar för enkel inloggning. Den **attributet mappning princip** används sedan för att matcha grupperna till önskad Rackspace roller och grupper:

    ![Grupperna anspråk inställningar](common/sso-groups-claim.png)

* Som standard skickar Azure AD UID för Azure AD-grupper i SAML-anspråket jämfört med namnet på gruppen. Om du synkroniserar din lokala Active Directory till Azure AD har du möjlighet att skicka de faktiska namnen på grupperna:

    ![Grupperna anspråk inställningar](common/sso-groups-claims-names.png)

I följande exempel **attributet mappning princip** visar:
1. Ställa in Rackspace användarens namn i `user.name` SAML-anspråk. Några anspråk kan användas, men det är vanligt att ställa in detta på ett fält som innehåller användarens e-postadress.
1. Ange rollerna Rackspace `admin` och `billing:admin` för en användare genom att matcha en Azure AD-grupp, efter namn eller grupp UID. En *ersättningen* av `"{0}"` i den `roles` fältet används och kommer att ersättas av resultatet av den `remote` regel uttryck.
1. Med hjälp av den `"{D}"` *standard ersättningen* så att Rackspace hämta ytterligare SAML-fält genom att söka efter standard och välkänd SAML-anspråk i SAML-exchange.

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
> Kontrollera att du använder en textredigerare som validerar YAML syntax när du redigerar principfilen.

Se Rackspace [attributet mappning grunderna dokumentation](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) fler exempel.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Rackspace SSO i åtkomstpanelen bör du vara automatiskt inloggad på Rackspace SSO som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också använda den **verifiera** knappen i den **Rackspace SSO** enkel inloggning inställningar:

   ![Verifieringsknapp för enkel inloggning](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

