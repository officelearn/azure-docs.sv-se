---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med CloudPassage | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0bcf0b4781e7ecb4301672f511a9f01f7fd2082
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561240"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med CloudPassage

I den här självstudien får du lära dig hur du integrerar CloudPassage med Azure Active Directory (Azure AD). När du integrerar CloudPassage med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CloudPassage.
* Gör att användarna automatiskt loggas in på CloudPassage med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* CloudPassage enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* CloudPassage har stöd för **SP**-initierad enkel inloggning

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-cloudpassage-from-the-gallery"></a>Lägga till CloudPassage från galleriet

För att konfigurera integreringen av CloudPassage i Azure AD måste du lägga till CloudPassage från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **CloudPassage** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **CloudPassage** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Konfigurera och testa en azure AD-inloggning för CloudPassage

Konfigurera och testa Azure AD SSO med CloudPassage med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i CloudPassage.

Så här konfigurerar och testar du Azure AD SSO med CloudPassage:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera CloudPassage SSO](#configure-cloudpassage-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa CloudPassage-testanvändare](#create-cloudpassage-test-user)** – om du vill ha en motsvarighet till B.Simon i CloudPassage som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **CloudPassage-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

     a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://portal.cloudpassage.com/saml/init/accountid`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://portal.cloudpassage.com/saml/consume/accountid`. Du kan hämta värdet för det här attributet genom att klicka på **SSO Setup documentation** (Dokumentation för konfiguration av enkel inloggning) i avsnittet **Inställningar för enkel inloggning** i CloudPassage-portalen.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [kundsupporten för CloudPassage](https://www.cloudpassage.com/company/contact/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. CloudPassage-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Förutom ovan förväntar sig CloudPassage-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | förnamn |user.givenname |
    | efternamn |user.surname |
    | e-post |user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera CloudPassage.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CloudPassage.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **CloudPassage**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cloudpassage-sso"></a>Konfigurera CloudPassage SSO

1. I ett annat webbläsarfönster loggar du in på din CloudPassage-företagsplats som administratör.

1. På menyn längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Konfigurera enkel inloggning][12]

1. Klicka på fliken **Autentiseringsinställningar**. 
   
    ![Konfigurera enkel inloggning][13]

1. I avsnittet **Inställningar för enkel inloggning** utför du följande steg: 
   
    ![Konfigurera enkel inloggning][14]

    a. Markera kryssrutan **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Aktivera enkel inloggning (SSO)(Dokumentation för konfiguration av enkel inloggning)).
    
    b. Klistra in **Azure AD-identifierare** i **SMS-textrutan för SAML-utfärdaren.**
  
    c. Klistra in **inloggnings-URL** i textrutan **SAML endpoint URL** (URL för SAML-slutpunkt).
  
    d. Klistra in **utloggnings-URL** i textrutan **Logout landing page** (landningssida för utloggning).
  
    e. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).
  
    f. Klicka på **Spara**.

### <a name="create-cloudpassage-test-user"></a>Skapa CloudPassage-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i CloudPassage.

**Så här skapar du en användare som heter B.Simon i CloudPassage:**

1. Logga in på din **CloudPassage**-företagsplats som administratör. 

1. I verktygsfältet längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Skapa en CloudPassage-testanvändare][22] 

1. Klicka på fliken **Användare** och sedan på **Lägg till ny användare**. 
   
    ![Skapa en CloudPassage-testanvändare][23]

1. I avsnittet **Lägg till ny användare** utför du följande steg: 
   
    ![Skapa en CloudPassage-testanvändare][24]
    
    a. I textrutan **Förnamn** skriver du Britta. 
  
    b. I textrutan **Efternamn** skriver du Simon.
  
    c. I textrutorna **Användarnamn**, **E-post** och **Ange e-postadressen igen** skriver du Brittas användarnamn i Azure AD.
  
    d. För **Åtkomsttyp** väljer du **Enable Halo Portal Access** (Aktivera Halo Portal-åtkomst).
  
    e. Klicka på **Lägg till**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CloudPassage-panelen i åtkomstpanelen bör du automatiskt loggas in på CloudPassage som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CloudPassage med Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

