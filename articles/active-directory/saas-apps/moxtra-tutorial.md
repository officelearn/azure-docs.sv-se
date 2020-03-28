---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Moxtra | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74889643"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Självstudiekurs: Azure Active Directory enkel inloggning (SSO) integration med Moxtra

I den här självstudien får du lära dig hur du integrerar Moxtra med Azure Active Directory (Azure AD). När du integrerar Moxtra med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Moxtra.
* Gör att användarna automatiskt loggas in på Moxtra med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Moxtra enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Moxtra stödjer **SP** initierade SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-moxtra-from-the-gallery"></a>Lägga till Moxtra från galleriet

Om du vill konfigurera integreringen av Moxtra i Azure AD måste du lägga till Moxtra från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Moxtra** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Moxtra** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Konfigurera och testa en azure AD-inloggning för Moxtra

Konfigurera och testa Azure AD SSO med Moxtra med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Moxtra.

Så här konfigurerar och testar du Azure AD SSO med Moxtra:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Moxtra SSO](#configure-moxtra-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Moxtra-testanvändare](#create-moxtra-test-user)** – om du vill ha en motsvarighet till B.Simon i Moxtra som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL i textrutan **Sign-on-URL:**`https://www.moxtra.com/service/#login`

1. Moxtra-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Moxtra ansökan få fler attribut som skall skickas tillbaka i SAML svar. I avsnittet Användaranspråk i dialogrutan Användarattribut utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ------------------- | -------------------- |    
    | förnamn | user.givenname |
    | efternamn | user.surname |
    | idpid (idpid)    | < Azure AD->

    > [!Note]
    > Värdet **för idpid-attributet** är inte verkligt. Du kan hämta det faktiska värdet från **Ställ in Moxtra-avsnittet** från steg#8. 

    1. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    1. Lämna **Namnrymd** tom.

    1. Välj Källa som **Attribut**.

    1. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    1. Klicka på **Ok**

    1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Moxtra** baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Moxtra.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Moxtra**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-moxtra-sso"></a>Konfigurera Moxtra SSO

1. I ett annat webbläsarfönster loggar du in på din Moxtra-företagswebbplats som administratör.

2. Klicka på Admin Console **> SAML Enkel inloggning**i verktygsfältet till vänster och klicka sedan på **Ny**.
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Gör följande på **SAML-sidan:**
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Skriv ett namn för **konfigurationen** (t.ex. *SAML* 
  
    b. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen i **IdP-entitets-ID.** 
 
    c. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen. 
 
    d. Skriv **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**i textrutan **AuthnContextClassRef** . 
 
    e. Skriv **urn:oasis:names:names:tc:SAML:1.1:nameid-format:emailAddress**i textrutan **NameID** Format: 
 
    f. Öppna certifikat som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet och klistra sedan in det i **textrutan för certifikat.**    
 
    g. Skriv din SAML-e-postdomändomän i SMS-postrutan FÖR SAML.    
  
    >[!NOTE]
    >Klicka på "**i**" nedan om du vill se stegen för att verifiera domänen.

    h. Klicka på **Uppdatera**.

### <a name="create-moxtra-test-user"></a>Skapa Moxtra-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.simon i Moxtra.

**Så här skapar du en användare som heter B.simon i Moxtra:**

1. Logga in på din Moxtra-företagswebbplats som administratör.

1. Klicka på Admin Console **> User Management i**verktygsfältet till vänster och lägg sedan till **användare**.
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. I dialogrutan **Lägg till användare** utför du följande steg:
  
    a. Skriv **B**i textrutan **Förnamn** .
  
    b. Skriv **Simon**i textrutan **Efternamn** .
  
    c. Skriv B.simons e-postadress på samma sätt som på Azure-portalen i **textrutan e-post.**
  
    d. Skriv **Dev**i textrutan **Division** .
  
    e. Skriv **IT**i textrutan **Avdelning** .
  
    f. Välj **Administratör**.
  
    g. Klicka på **Lägg till**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Moxtra på åtkomstpanelen ska du automatiskt loggas in på den Moxtra som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Moxtra med Azure AD](https://aad.portal.azure.com/)

