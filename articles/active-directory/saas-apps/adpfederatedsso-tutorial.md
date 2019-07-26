---
title: 'Självstudier: Azure Active Directory-integrering med ADP | Microsoft Docs'
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
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1597a4ca9cac7ba3885e863502f156d4c83aeed1
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516378"
---
# <a name="tutorial-integrate-adp-with-azure-active-directory"></a>Självstudier: Integrera ADP med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar ADP med Azure Active Directory (Azure AD). När du integrerar ADP med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ADP.
* Gör det möjligt för användarna att logga in automatiskt till ADP med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggnings prenumeration med ADP (enkel inloggning).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ADP stöder **IDP**-initierad enkel inloggning

## <a name="adding-adp-from-the-gallery"></a>Lägga till ADP från galleriet

För att konfigurera integreringen av ADP i Azure AD måste du lägga till ADP från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **ADP** i sökrutan.
1. Välj **ADP** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med ADP med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ADP.

Om du vill konfigurera och testa Azure AD SSO med ADP, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ADP SSO](#configure-adp-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa ADP-test användare](#create-adp-test-user)** – för att få en motsvarighet till B. Simon i ADP som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b. Kopiera **URL:en för användaråtkomst** som du sedan ska klistra in i avsnittet **Konfigurera inloggnings-URL**, som beskrivs senare i självstudien.

    c. Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    d. Ange värdet för fältet **Synligt för användare** till **Nej**.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ADP** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://fed.adp.com`

5. ADP-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut. Anspråks namnet är alltid **PersonImmutableID** och värdet som vi visade för att mappa med **Anställningsnr**.

    Användarmappningen från Azure AD till ADP görs baserat på **employeeid**, men du kan mappa detta till ett annat värde baserat på dina programinställningar. Vi arbetar med att först använda [ADP-support teamet](https://www.adp.com/contact-us/overview.aspx) för att använda rätt identifierare för en användare och mappa värdet med **PersonImmutableID** -anspråket.

    ![image](common/edit-attribute.png)

6. Förutom över, förväntar ADP-program några fler attribut att skickas tillbaka i SAML-svar. I avsnittet användar anspråk i dialog rutan användarattribut, utför följande steg för att lägga till SAML-token-attributet som visas i tabellen nedan: 

    | Namn | Källattribut|
    | ---------------| --------- |
    | PersonImmutableID  | user.employeeid |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-försäkran måste du kontakta [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) och be om värdet för det unika användaridentifierarattributet för din klientorganisation. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program. 

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera ADP** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-adp-sso"></a>Konfigurera ADP SSO

För att konfigurera enkel inloggning på **ADP**-sidan måste du ladda upp den nedladdade **XML:en med metadata** till [ADP-webbplatsen](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta några dagar.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP-tjänster för federerad åtkomst

>[!Important]
> Anställda som behöver federerad åtkomst till dina ADP-tjänster måste tilldelas till ADP-tjänstappen, och därefter måste användarna tilldelas till den specifika ADP-tjänsten.
När du har fått en bekräftelse av din ADP-representant konfigurerar du dina ADP-tjänster och tilldelar/hanterar användare för att styra användaråtkomsten till den specifika ADP-tjänsten.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **ADP** i sökrutan.
1. Välj **ADP** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.
1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg:  

    ![Länkade egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b.  Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    c.  Ange värdet för fältet **Synligt för användare** till **Ja**.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ADP** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.

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

9. **Spara**  dina ändringar.

10. När du har fått en bekräftelse från din ADP-representant börjar du testa med en eller två användare.

    a. Tilldela några användare till ADP-tjänstappen för att testa federerad åtkomst.

    b. Testningen lyckas om användarna kommer åt ADP-tjänstappen i galleriet och kan komma åt ADP-tjänsten.
 
11. När du har bekräftat att testningen lyckades tilldelar du den federerade ADP-tjänsten till enskilda användare eller användargrupper, vilket beskrivs senare i självstudien och distribuerar den till dina anställda.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ADP.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **ADP**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adp-test-user"></a>Skapa ADP-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i ADP. Arbeta med [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) för att lägga till användarna till ADP-kontot. 

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på ADP-panelen på åtkomstpanelen bör du automatiskt loggas in i ADP-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

