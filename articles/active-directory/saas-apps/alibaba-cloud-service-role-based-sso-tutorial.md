---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Alibaba Cloud Service (Roll-based SSO) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Alibaba Cloud Service (Rollbaserad SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22bec224d185d0306f2b0032aef929f627c910e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77367936"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Alibaba Cloud Service (rollbaserad SSO)

I den här självstudien får du lära dig hur du integrerar Alibaba Cloud Service (Rollbaserad SSO) med Azure Active Directory (Azure AD). När du integrerar Alibaba Cloud Service (rollbaserad SSO) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Alibaba Cloud Service (Rollbaserad SSO).
* Gör att användarna automatiskt loggas in på Alibaba Cloud Service (Rollbaserad SSO) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Alibaba Cloud Service (Roll-based SSO) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Alibaba Cloud Service (rollbaserad SSO) stöder **IDP-initierad** SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Lägga till Alibaba Cloud Service (rollbaserad SSO) från galleriet

Om du vill konfigurera integreringen av Alibaba Cloud Service (rollbaserad SSO) i Azure AD måste du lägga till Alibaba Cloud Service (rollbaserad SSO) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Alibaba Cloud Service (Rollbaserad SSO)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Alibaba Cloud Service (Rollbaserad SSO)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.
5. På sidan **Alibaba Cloud Service (Roll-based SSO)** klickar du på **Egenskaper** i det vänstra navigeringsfönstret och kopierar **objekt-ID:t** och sparar det på datorn för efterföljande användning.

    ![Egenskaper config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Konfigurera och testa en enda Azure AD-inloggning för Alibaba Cloud Service (rollbaserad SSO)

Konfigurera och testa Azure AD SSO med Alibaba Cloud Service (Rollbaserad SSO) med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Alibaba Cloud Service (Rollbaserad SSO).

Så här konfigurerar och testar du Azure AD SSO med Alibaba Cloud Service (Rollbaserad SSO) slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera rollbaserad enkel inloggning i Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – så att användarna kan använda den här funktionen.
    1. **[Konfigurera Alibaba Cloud Service (Roll-based SSO) SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Alibaba Cloud Service (Roll-based SSO) testanvändare](#create-alibaba-cloud-service-role-based-sso-test-user)** - att ha en motsvarighet till Britta Simon i Alibaba Cloud Service (Roll-baserade SSO) som är kopplad till Azure AD representation av användaren.
3. **[Testa enstaka SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Alibaba Cloud Service (Roll-based SSO)** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får tjänsteleverantörens metadata från den här [webbadressen](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp fylls url-värdena **identifierare** och **svar** i automatiskt i textrutan Alibaba Cloud Service (Rollbaserad SSO):

    > [!Note]
    > Om **URL-värdena identifierare** och **Svara** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Alibaba Cloud Service (Rollbaserad SSO) kräver att roller konfigureras i Azure AD. Rollanspråket är förkonfigurerat så att du inte behöver konfigurera det, men du måste ändå skapa dem i Azure AD med den här [artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Alibaba Cloud Service (Roll-based SSO).**

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Alibaba Cloud Service (Rollbaserad SSO).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Alibaba Cloud **Service (Roll-based SSO)** i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. På fliken **Användare och grupper** väljer du u2 i användarlistan och klickar på **Välj**. Klicka sedan på **Tilldela**.

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Visa den tilldelade rollen och testa Alibaba Cloud Service (Rollbaserad SSO).

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >När du har tilldelat användaren (u2) kopplas den skapade rollen automatiskt till användaren. Om du har skapat flera roller måste du koppla lämplig roll till användaren efter behov. Om du vill implementera rollbaserad SSO från Azure AD till flera Alibaba Cloud-konton upprepar du föregående steg.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurera rollbaserad enkel inloggning i Alibaba Cloud Service

1. Logga in på Alibaba Cloud [RAM-konsolen](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) med konto1.

2. Välj **SSO**i det vänstra navigeringsfönstret .

3. Klicka på **Skapa IdP**på fliken **Rollbaserad SSO** .

4. På den visade sidan `AAD` anger du i fältet IdP-namn, anger en beskrivning i fältet **Anteckning,** klickar på **Ladda** upp för att ladda upp federationsmetadatafilen som du hämtade tidigare och klickar på **OK**.

5. När IdP har skapats klickar du på **Skapa RAM-roll**.

6. Välj `AADrole` `AAD` i **listrutan Välj IdP** i fältet **RAM-rollnamn** och klicka på OK.

    >[!NOTE]
    >Du kan bevilja behörighet till rollen efter behov. När du har skapat IdP och motsvarande roll rekommenderar vi att du sparar IdP:s ARN och rollen för efterföljande användning. Du kan hämta ARN på informationssidan för IDP och sidan med rollinformation.

7. Associera Alibaba Cloud RAM-rollen (AADrole) med Azure AD-användaren (u2): Om du vill associera RAM-rollen med Azure AD-användaren måste du skapa en roll i Azure AD genom att följa dessa steg:

    a. Logga in på [Utforskaren](https://developer.microsoft.com/graph/graph-explorer)i Microsoft Graph .

    b. Klicka på **ändra behörigheter** för att få nödvändiga behörigheter för att skapa en roll.

    ![Graf config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Markera följande behörigheter i listan och klicka på **Ändra behörigheter**, som visas i följande bild.

    ![Graf config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >När behörigheter har beviljats loggar du in på Graph Explorer igen.

    d. På sidan Diagramutforskaren väljer du **HÄMTA** från den första listrutan och **betaversionen** i den andra listrutan. Ange `https://graph.microsoft.com/beta/servicePrincipals` sedan i fältet bredvid listrutorna och klicka på **Kör fråga**.

    ![Graf config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Om du använder flera kataloger `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` kan du ange i fältet för frågan.

    e. I avsnittet **Förhandsgranskning** av svar extraherar du egenskapen appRoles från "Tjänsthuvudnamn" för efterföljande användning.

    ![Graf config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Du kan hitta egenskapen appRoles genom att ange `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` i fältet för frågan. Observera att `objectID` det är det objekt-ID som du har kopierat från sidan Azure **AD-egenskaper.**

    f. Gå tillbaka till Graph Explorer, ändra metoden från **GET** till **PATCH,** klistra in följande innehåll i avsnittet **Begäran brödtext** och klicka på **Kör fråga:**
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > Det `value` är IDP:s ARN och den roll som du skapade i RAM-konsolen. Här kan du lägga till flera roller efter behov. Azure AD skickar värdet för dessa roller som anspråksvärde i SAML-svar. Du kan dock bara lägga `msiam_access` till nya roller efter delen för korrigeringsåtgärden. För att underlätta skapandeprocessen rekommenderar vi att du använder en ID-generator, till exempel GUID Generator, för att generera ID i realtid.

    g. När "Tjänsthuvudstadsnamn" har korrigerats med den nödvändiga rollen bifogar du rollen med Azure AD-användaren (u2) genom att följa stegen i Avsnittet **Tilldela Azure AD-testanvändarn** i självstudien.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurera Alibaba Cloud Service (rollbaserad SSO) SSO

Om du vill konfigurera enkel inloggning på **Alibaba Cloud Service -sidan (Roll-based SSO)** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Alibaba Cloud Service (Roll-based SSO) supportteam](https://www.aliyun.com/service/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Skapa Alibaba Cloud Service (rollbaserad SSO)-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Alibaba Cloud Service (Rollbaserad SSO). Arbeta med [Alibaba Cloud Service (Roll-based SSO) supportteam](https://www.aliyun.com/service/) för att lägga till användare i Alibaba Cloud Service (Roll-based SSO) plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

När föregående konfigurationer har slutförts testar du Alibaba Cloud Service (Rollbaserad SSO) genom att följa dessa steg:

1. Gå till sidan Alibaba **Cloud Service (Roll-based SSO)** på **Azure-portalen,** välj Enkel inloggning och klicka på **Testa**.

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klicka på **Logga in aktuell användare**.

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Välj u2 på sidan för kontoval.

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Följande sida visas, vilket anger att rollbaserad SSO lyckas.

    ![Testa config](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Alibaba Cloud Service (rollbaserad SSO) med Azure AD](https://aad.portal.azure.com/)

