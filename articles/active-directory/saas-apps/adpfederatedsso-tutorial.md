---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ADP | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70162662"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ADP

I den här självstudien får du lära dig hur du integrerar ADP med Azure Active Directory (Azure AD). När du integrerar ADP med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ADP.
* Aktivera dina användare så att de automatiskt loggas in på ADP med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* ADP enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* ADP stöder **IDP**-initierad enkel inloggning

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-adp-from-the-gallery"></a>Lägga till ADP från galleriet

För att konfigurera integreringen av ADP i Azure AD måste du lägga till ADP från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ADP** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ADP** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Konfigurera och testa en azure AD-inloggning för ADP

Konfigurera och testa Azure AD SSO med ADP med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ADP.

Så här konfigurerar och testar du Azure AD SSO med ADP:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera ADP SSO](#configure-adp-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa ADP-testanvändare](#create-adp-test-user)** - om du vill ha en motsvarighet till B.Simon i ADP som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b. Kopiera **URL:en för användaråtkomst** som du sedan ska klistra in i avsnittet **Konfigurera inloggnings-URL**, som beskrivs senare i självstudien.

    c. Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    d. Ange värdet för fältet **Synligt för användare** till **Nej**.

1. Leta reda på avsnittet **ADP** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://fed.adp.com`

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser i avsnittet **Konfigurera ADP** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ADP.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **ADP**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-adp-sso"></a>Konfigurera ADP SSO

För att konfigurera enkel inloggning på **ADP**-sidan måste du ladda upp den nedladdade **XML:en med metadata** till [ADP-webbplatsen](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta några dagar.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP-tjänster för federerad åtkomst

>[!Important]
> Anställda som behöver federerad åtkomst till dina ADP-tjänster måste tilldelas till ADP-tjänstappen, och därefter måste användarna tilldelas till den specifika ADP-tjänsten.
När du har fått en bekräftelse av din ADP-representant konfigurerar du dina ADP-tjänster och tilldelar/hanterar användare för att styra användaråtkomsten till den specifika ADP-tjänsten.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ADP** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ADP** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.
1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg:  

    ![Enstaka inloggningslänkade egenskaper](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b.  Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    c.  Ange värdet för fältet **Synligt för användare** till **Ja**.

1. Leta reda på avsnittet **ADP** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.

1. Välj **Läge** som **Länkat** i dialogrutan **Välj en metod för enkel inloggning**. för att länka ditt program till **ADP**.

    ![Länkat för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navigera till avsnittet **Konfigurera inloggnings-URL** och utför följande steg:

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Klistra in **URL:en för användaråtkomst**, som du har kopierat från fliken **Egenskaper** ovan (från ADP-huvudappen).
                                                             
    b. Här är de fem appar som stöder olika **URL:er för vidarebefordringsstatus**. Du måste lägga till lämpligt värde för **URL:en för vidarebefordringsstatus** för ett visst program manuellt i **URL:en för användaråtkomst**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Spara ** dina ändringar.

10. När du har fått en bekräftelse från din ADP-representant börjar du testa med en eller två användare.

    a. Tilldela några användare till ADP-tjänstappen för att testa federerad åtkomst.

    b. Testningen lyckas om användarna kommer åt ADP-tjänstappen i galleriet och kan komma åt ADP-tjänsten.
 
11. När du har bekräftat att testningen lyckades tilldelar du den federerade ADP-tjänsten till enskilda användare eller användargrupper, vilket beskrivs senare i självstudien och distribuerar den till dina anställda.

### <a name="create-adp-test-user"></a>Skapa ADP-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i ADP. Arbeta med [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) för att lägga till användarna till ADP-kontot. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ADP-panelen på åtkomstpanelen bör du automatiskt loggas in i ADP-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ADP med Azure AD](https://aad.portal.azure.com)
