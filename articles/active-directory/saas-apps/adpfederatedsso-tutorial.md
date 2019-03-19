---
title: 'Självstudier: Azure Active Directory-integrering med ADP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eba63f8295fb5bebffdc8480f763c852521e331b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880916"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Självstudier: Azure Active Directory-integrering med ADP

I den här självstudien får du lära dig hur du integrerar ADP med Azure Active Directory (AD Azure).
Integreringen av ADP med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till ADP från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i ADP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ADP behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ADP-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ADP stöder **IDP**-initierad enkel inloggning

## <a name="adding-adp-from-the-gallery"></a>Lägga till ADP från galleriet

För att konfigurera integreringen av ADP i Azure AD måste du lägga till ADP från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ADP från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ADP**, väljer **ADP** från resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ADP i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med ADP baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ADP upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med ADP måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för ADP](#configure-adp-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ADP-testanvändare](#create-adp-test-user)**  – för att skapa en motsvarighet till Britta Simon i ADP som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med ADP:

1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b. Kopiera **URL:en för användaråtkomst** som du sedan ska klistra in i avsnittet **Konfigurera inloggnings-URL**, som beskrivs senare i självstudien.

    c. Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    d. Ange värdet för fältet **Synligt för användare** till **Nej**.

2. Välj **Enkel inloggning** på sidan för programintegrering med **ADP** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

3. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

5. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med ADP-domäner och -URL:er](common/idp-identifier.png)

    I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://fed.adp.com`

6. ADP-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**. Anspråkets namn kommer alltid att vara **”PersonImmutableID”** och värdet som mappas med **employeeid**. 

    Användarmappningen från Azure AD till ADP görs baserat på **employeeid**, men du kan mappa detta till ett annat värde baserat på dina programinställningar. Kontakta [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) först så att du är säker på att du använder rätt identifierare för en användare och mappa det värdet med **”PersonImmutableID”**-anspråket.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:
    
    | Namn | Källattribut | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-försäkran måste du kontakta [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) och be om värdet för det unika användaridentifierarattributet för din klientorganisation. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program. 

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Konfigurera enkel inloggning för ADP

För att konfigurera enkel inloggning på **ADP**-sidan måste du ladda upp den nedladdade **XML:en med metadata** till [ADP-webbplatsen](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta några dagar.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP-tjänster för federerad åtkomst

>[!Important]
> Anställda som behöver federerad åtkomst till dina ADP-tjänster måste tilldelas till ADP-tjänstappen, och därefter måste användarna tilldelas till den specifika ADP-tjänsten.
När du har fått en bekräftelse av din ADP-representant konfigurerar du dina ADP-tjänster och tilldelar/hanterar användare för att styra användaråtkomsten till den specifika ADP-tjänsten.

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ADP**, väljer **ADP** från resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ADP i resultatlistan](common/search-new-app.png)

5. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg:  

    ![Enkel inloggning för länkade egenskaper](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b.  Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    c.  Ange värdet för fältet **Synligt för användare** till **Ja**.

6. Välj **Enkel inloggning** på sidan för programintegrering med **ADP** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

7. Välj **Läge** som **Länkat** i dialogrutan **Välj en metod för enkel inloggning**. för att länka ditt program till **ADP**.

    ![Länkat för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navigera till avsnittet **Konfigurera inloggnings-URL** och utför följande steg:

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Klistra in **URL:en för användaråtkomst**, som du har kopierat från fliken **Egenskaper** ovan (från ADP-huvudappen).
                                                             
    b. Här är de fem appar som stöder olika **URL:er för vidarebefordringsstatus**. Du måste lägga till lämpligt värde för **URL:en för vidarebefordringsstatus** för ett visst program manuellt i **URL:en för användaråtkomst**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Spara**  dina ändringar.

10. När du har fått en bekräftelse från din ADP-representant börjar du testa med en eller två användare.

    a. Tilldela några användare till ADP-tjänstappen för att testa federerad åtkomst.

    b. Testningen lyckas om användarna kommer åt ADP-tjänstappen i galleriet och kan komma åt ADP-tjänsten.
 
11. När du har bekräftat att testningen lyckades tilldelar du den federerade ADP-tjänsten till enskilda användare eller användargrupper, vilket beskrivs senare i självstudien och distribuerar den till dina anställda.
 
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till ADP.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **ADP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver du och väljer **ADP**.

    ![ADP-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adp-test-user"></a>Skapa ADP-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i ADP. Arbeta med [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) för att lägga till användarna till ADP-kontot. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på ADP-panelen på åtkomstpanelen bör du automatiskt loggas in i ADP-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

