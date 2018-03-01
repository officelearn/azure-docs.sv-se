---
title: Skydda anpassade kopplingar med Azure AD - Azure Logic Apps | Microsoft Docs
description: "Lägg till säkerhet och autentisering för API och anslutningsapp med Azure Active Directory (Azure AD)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Lägger till säkerhet i API och anslutningen med Azure Active Directory (AD Azure)

Skydda anrop mellan ditt API och din anpassade anslutningsapp genom att lägga till Azure AD-autentisering för ditt webb-API och din anslutningsapp. För det här scenariot skapar du två Azure Active Directory-appar (Azure AD) – en Azure AD-app skyddar ditt webb-API medan den andra Azure AD-appen skyddar registreringen av anslutningsappen och lägger till delegerad åtkomst. 

I den här självstudiekursen används Azure Resource Manager-API:et som exempel. Azure Resource Manager hjälper dig att hantera komponenterna för en lösning som du har skapat i Azure, till exempel databaser, virtuella datorer och webbappar. En anpassad anslutningsapp för Azure Resource Manager kan vara användbar när du vill hantera Azure-resurser från dina arbetsflöden. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du registrera dig för en [prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* För den här självstudiekursen: [ OpenAPI-exempelfilen för Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > OpenAPI-exempelfilen definierar inte alla Azure Resource Manager-åtgärder och innehåller just nu bara åtgärderna för [Lista alla prenumerationer](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Du kan redigera detta OpenAPI eller skapa en annan OpenAPI-fil med [onlineredigeraren för OpenAPI](http://editor.swagger.io/).
  >
  > Du kan använda den här självstudiekursen på valfritt RESTful-API där du vill använda Azure AD-autentisering.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Skapa din första Azure AD-app för att skydda ditt webb-API

Den första Azure AD-appen utför autentisering när din anpassade anslutningsapp anropar API:et, som i det här exemplet är Azure Resource Manager-API.

1. Logga in på [Azure Portal](https://portal.azure.com). Om du har fler än en Azure Active Directory-klient bekräftar du att du är inloggad på rätt katalog genom att kontrollera katalogen under ditt användarnamn. 

   ![Bekräfta katalogen](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Om du vill ändra katalog väljer du ditt användarnamn och väljer sedan den katalog du vill använda.

2. På huvudmenyn för Azure väljer du **Azure Active Directory** så att du kan visa din aktuella historik.

   ![Välj ”Azure Active Directory”](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Om Azure huvudmenyn inte visas **Azure Active Directory**, Välj **alla tjänster**. Skriv ”Azure Active Directory” i rutan **Filter** när du filtrerar och välj sedan **Azure Active Directory**.

3. På katalogmenyn, under **Hantera** väljer du **Appregistreringar**. I listan med registerade appar väljer du **+ Ny programregistrering**.

   ![Välj ”Appregistreringar”, ”+ Ny programregistrering"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. Under **Skapa** anger du informationen för din Azure AD-app enligt beskrivningen i tabellen. Välj sedan **Skapa**. 

   ![Skapa Azure AD-app](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *webb-api-appnamn* | Namnet på webb-API:ets Azure AD-app | 
   | **Programtyp** | **Webbapp/API** | Appens typ | 
   | **Inloggnings-URL** | `https://login.windows.net` | | 
   |||| 

5. När du återgår till katalogens lista över **appregistreringar** väljer du din Azure AD-app.

   ![Välj din Azure AD i listan](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. När appens information visas ska du **kopiera och spara appens *Program-ID* på en säker plats**. Du behöver detta ID senare.

   ![Spara program-ID för senare användning](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Ange nu en svars-URL för Azure AD-appen. I appens **Inställningar**-meny väljer du **Svars-URL:er**. Ange URL:en och välj sedan **Spara**.

   ![Svars-URL:er](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Svars-URL:er** | Ange följande URL för ditt eget API: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Delegerade behörigheter** | {inte nödvändigt} | | 
   | **Klientnyckel** | {inte nödvändigt} | | 
   |||| 

   > [!TIP]
   > Om menyn **Inställningar** inte visades tidigare väljer du **Inställningar** här:
   >
   > ![Välj ”Inställningar”](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Skapa en andra Azure AD-app för din anpassade anslutningsapp

Din andra Azure AD-app skyddar registreringen av den anpassade appen och lägger till delegerad åtkomst till webb-API:et som skyddas av den första Azure AD-appen. 

> [!IMPORTANT]
> Kontrollera att båda Azure AD-apparna finns i samma katalog.

1. Återgå till listan **Appregistreringar** och välj **+ Ny programregistrering** igen.

   ![Välj ”Appregistreringar”, ”+ Ny programregistrering"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. Under **Skapa** anger du informationen för din andra Azure AD-app enligt beskrivningen i tabellen. Välj sedan **Skapa**. 

   ![Skapa Azure AD-app](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *namn-på-din-anslutningsapp* | Namnet på anslutningsappens Azure AD-app | 
   | **Programtyp** | **Webbapp/API** | Appens typ | 
   | **Inloggnings-URL** | `https://login.windows.net` | | 
   |||| 

3. När du återgår till katalogens lista över **appregistreringar** väljer du din andra Azure AD-app.

   ![Välj din andra Azure AD-app i listan](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. När appens information visas ska du även **kopiera och spara appens *Program-ID* på en säker plats**. Du behöver detta ID senare.

   ![Spara program-ID för senare användning](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Ange nu en svars-URL för Azure AD-appen. I appens **Inställningar**-meny väljer du **Svars-URL:er**. Ange URL:en och välj sedan **Spara**.

   | Inställning | Föreslaget värde | 
   | ------- | --------------- | 
   | **Svars-URL:er** | För den anpassade Azure Resource Manager-anslutningsappen anger du följande URL: `https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Om menyn **Inställningar** inte visades tidigare väljer du **Inställningar** här:
   >
   > ![Välj ”Inställningar”](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. I menyn **Inställningar** väljer du **Nödvändiga behörigheter** > **Lägg till**.

   ![Nödvändiga behörigheter > Lägg till](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. När menyn **Lägg till API-åtkomst** öppnas väljer du **Välj en API** > 
**Windows Azure Service Management-API** > **Välj**.

   ![Välj ett API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. På **Lägg till API-åtkomst** väljer du **Välj behörigheter**. Under **Delegerade behörigheter** väljer du **Åtkomst till Azure Service Management som organisationsanvändare** > **Välj**.

   ![Välj ”Delegerade behörigheter” > ”Åtkomst till Azure Service Management som organisationsanvändare”](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Annars, för andra alternativ:

   | Alternativ | Föreslaget värde | Beskrivning | 
   | ------ | --------------- | ----------- | 
   | **Delegerade behörigheter** | | Välj behörigheter för delegerad åtkomst till ditt webb-API | 
   |||| 

9. På menyn **Lägg till API-åtkomst** väljer du nu **Klart**.

   ![Menyn ”Lägg till API-åtkomst” > ”Klart”](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Generera nu en *klientnyckel* eller ”hemlighet” för anslutningsappens Azure AD-app. 

    1. På menyn **Inställningar** väljer du **Nycklar**. 
    Ange ett namn på nyckeln med max. 16 tecken, välj en förfalloperiod och välj sedan **Spara**.

       ![Skapa en klientnyckel](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. När din genererade nyckel visas **ska du kopiera och spara nyckeln på en säker plats** innan du lämnar sidan **Nycklar**.
    
       ![Kopiera och spara nyckeln manuellt](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. När du har sparat nyckeln kan du stänga menyn **Inställningar**.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Lägga till Azure AD-autentisering för din webb-API-app

Aktivera nu autentisering för ditt webb-API med din första Azure AD-app. Mer information lär du dig [hur du konfigurerar din App tjänstprogram att använda Azure Active Directory-inloggningen](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. I den [Azure-portalen](https://portal.azure.com), hitta Web API-appen som du tidigare har publicerats i [skapa egna kopplingar från Web API: er](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. Under **Inställningar** väljer du **Autentisering/auktorisering**. 

   1. Under **App Service-autentisering** väljer du **På**.
   2. För **Åtgärd att vidta när en begäran inte har autentiserats** väljer du **Logga in med Azure Active Directory**.
   3. För **Autentiseringsprovidrar** väljer du **Azure Active Directory**. 

   ![Välj ”Autentisering/auktorisering”](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. På sidan **Azure Active Directory-inställningar**:

   1. Under **Hanteringsläge** väljer du **Express**.

   2. Välj nu den Azure AD-app som din webb-API-app använder för autentisering. 
   Välj **Välj befintligt AD-program** > **Azure AD-app**.

   3. Under **Azure AD-program** väljer du den Azure AD-app som du skapade tidigare för din webb-API-app. 
   
   4. Välj **OK** tills du kommer tillbaka till sidan **Autentisering/auktorisering**.

   Exempel:

   ![Välj den Azure AD-app som representerar din webb-API-app](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. På sidan **Autentisering/auktorisering** väljer du **Spara**.

   ![Spara autentiseringsinställningar](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Nu kan webb-API-appen använda Azure AD för autentisering.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Konfigurera Azure AD-autentisering i webb-API:ets OpenAPI-fil

Öppna OpenAPI-filen för din webb-API-app så att du kan lägga till `securityDefintions`-objektet och Azure AD-autentisering under egenskapen `host`. Här är ett exempel som visar egenskapen `host` och `securityDefinitions`-objektet:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Nu är du redo att skapa och registrera din anpassade anslutningsapp.

## <a name="next-steps"></a>Nästa steg

* [Registrera din anslutningsapp](../logic-apps/logic-apps-custom-connector-register.md)
* [Vanliga frågor och svar för anpassade anslutningsappar](../logic-apps/custom-connector-faq.md)
