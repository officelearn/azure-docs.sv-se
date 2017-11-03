---
title: Skydda anpassade kopplingar med Azure AD - Azure Logic Apps | Microsoft Docs
description: "Lägger till säkerhet och autentisering i API och anslutningen med Azure Active Directory (AD Azure)"
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
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Lägger till säkerhet i API och anslutningen med Azure Active Directory (AD Azure)

Lägg till Azure AD authentication till Web-API och din kontakt om du vill skydda anrop mellan API och anpassad koppling. Det här scenariot kan du skapa två appar i Azure Active Directory (AD Azure) - en Azure AD app skyddar dina webb-API, medan andra Azure AD-appar skyddar din registreringen av anslutningsverktyget och lägger till delegerad åtkomst. 

Den här kursen används Azure Resource Manager API som exempel. Azure Resource Manager hjälper dig att hantera komponenter för en lösning som du har skapat i Azure, t.ex databaser, virtuella datorer och webbappar. En anpassad koppling för Azure Resource Manager kan vara användbar när du vill hantera Azure-resurser från dina arbetsflöden. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du starta med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan registrera dig för en [betala per användning prenumeration](https://azure.microsoft.com/pricing/purchase-options/).

* Den här självstudien den [exempelfilen OpenAPI för Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > OpenAPI exempelfilen definierar inte alla åtgärder i Azure Resource Manager och innehåller för närvarande endast åtgärden för [lista över alla prenumerationer](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Du kan redigera den här OpenAPI eller skapa en annan OpenAPI fil med hjälp av den [online OpenAPI editor](http://editor.swagger.io/).
  >
  > Du kan använda den här självstudiekursen för RESTful-API som där du vill använda Azure AD-autentisering.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Skapa din första Azure AD-app för att skydda dina webb-API

Din första Azure AD-app utförs en autentisering när en anpassad koppling anropar API, vilket är Azure Resource Manager API i det här exemplet.

1. Logga in på [Azure Portal](https://portal.azure.com). Om du har mer än en Azure Active Directory-klient kan du bekräfta att du är inloggad på rätt katalog genom att kontrollera katalog under ditt användarnamn. 

   ![Bekräfta din katalog](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Om du vill ändra kataloger, Välj ditt användarnamn och välj den katalog som du vill.

2. Välj på Azure Huvudmeny, **Azure Active Directory** så att du kan visa den aktuella katalogen.

   ![Välj ”Azure Active Directory”](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Om Azure huvudmenyn inte visas **Azure Active Directory**, Välj **fler tjänster**. I den **Filter** rutan, Skriv ”Azure Active Directory” som filter och välja **Azure Active Directory**.

3. På menyn directory under **hantera**, Välj **App registreringar**. Välj i listan över registrerade appar **+ ny appregistrering**.

   ![Välj ”App registreringar”, ”+ ny appregistrering”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. Under **skapa**, ange information för din Azure AD-app som beskrivs i tabellen, och välj sedan **skapa**. 

   ![Skapa Azure AD-app](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *Web api-appens namn-* | Namn för Web API Azure AD-appar | 
   | **Programtyp** | **Webbprogrammet / API** | Typ av din app | 
   | **Inloggnings-URL** | `https://login.windows.net` | | 
   |||| 

5. När du kommer tillbaka till din katalog **App registreringar** väljer du din Azure AD-app.

   ![Välj Azure AD-appar från listan](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. När den appinformationssidan visas, kontrollerar du att du **kopiera och spara appens *program-ID* någonstans säker**. Du behöver detta ID för senare användning.

   ![Spara ”program-ID” för senare användning](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Ger nu en reply-URL för din Azure AD-app. I appens **inställningar** -menyn, Välj **Reply URL: er**. Ange URL: en, och välj sedan **spara**.

   ![Svars-URL: er](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Svars-URL: er** | Ange URL: en för din egen API: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Delegerade behörigheter** | {inte nödvändiga} | | 
   | **Nyckel för klienten** | {inte nödvändiga} | | 
   |||| 

   > [!TIP]
   > Om den **inställningar** förekommer inte tidigare menyn, Välj **inställningar** här:
   >
   > ![Välj ”inställningar”](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Skapa andra Azure AD-appar för din anpassade connector

Andra Azure AD-appar skyddar anpassad koppling registreringen och lägger till delegerad åtkomst till webb-API som skyddas av den första Azure AD-appen. 

> [!IMPORTANT]
> Kontrollera att både dina Azure AD-appar finns i samma katalog.

1. Gå tillbaka till den **App registreringar** listan, och välj **+ ny appregistrering** igen.

   ![Välj ”App registreringar”, ”+ ny appregistrering”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. Under **skapa**, ange detaljer för andra Azure AD-appar som beskrivs i tabellen och välj sedan **skapa**. 

   ![Skapa Azure AD-app](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *din Kopplingsnamn* | Namn för din anslutningstjänst Azure AD-appar | 
   | **Programtyp** | **Webbprogrammet / API** | Typ av din app | 
   | **Inloggnings-URL** | `https://login.windows.net` | | 
   |||| 

3. När du kommer tillbaka till din katalog **App registreringar** väljer du din andra Azure AD-app.

   ![Välj din andra Azure AD-app från listan](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. När den appinformationssidan visas, kontrollerar du att du även **kopiera och spara den här appen *program-ID* någonstans säker för**. Du behöver detta ID för senare användning.

   ![Spara ”program-ID” för senare användning](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Ger nu en reply-URL för din Azure AD-app. I appens **inställningar** -menyn, Välj **Reply URL: er**. Ange URL: en, och välj sedan **spara**.

   | Inställning | Föreslaget värde | 
   | ------- | --------------- | 
   | **Svars-URL: er** | Ange URL: en för den anpassade Azure Resource Manager-anslutningen:`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Om den **inställningar** förekommer inte tidigare menyn, Välj **inställningar** här:
   >
   > ![Välj ”inställningar”](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. I den **inställningar** -menyn, Välj **nödvändiga behörigheter** > **Lägg till**.

   ![Nödvändiga behörigheter > Lägg till](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. När den **lägga till API-åtkomst** menyn öppnas, Välj **väljer en API** > 
**Windows Azure Service Management API** > **Välj **.

   ![Välj en API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. På den **lägga till API-åtkomst** -menyn, Välj **Välj behörigheter**. Under **delegerad behörighet**, Välj **åtkomst till Azure Service Management som organisationen användare** > **Välj**.

   ![Välj ”delegerade behörigheter” > ”åtkomst Azure Services Management som organisationen användare”](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Annars för andra alternativ:

   | Alternativ | Föreslaget värde | Beskrivning | 
   | ------ | --------------- | ----------- | 
   | **Delegerade behörigheter** | | Välj behörigheter för delegerad åtkomst till webb-API | 
   |||| 

9. Nu på den **lägga till API-åtkomst** -menyn väljer du **klar**.

   ![”Lägg till API-åtkomst” menyn > ”klar”](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Nu genererar en *klientnyckel*, eller ”hemliga”, för din anslutningstjänst Azure AD-appar. 

    1. Tillbaka på den **inställningar** -menyn, Välj **nycklar**. 
    Ange ett namn för din nyckel med färre än 16 tecken, Välj en förfalloperiod och välj sedan **spara**.

       ![Skapa en nyckel för klienten](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. När din genererad nyckel visas **se till att du kopiera och spara den här nyckeln någonstans säker** innan du lämnar den **nycklar** sidan.
    
       ![Manuellt kopiera och spara din nyckel](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. När du har sparat din nyckel, du kan stänga den **inställningar** menyn.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Lägg till Azure AD-autentisering i Web API-appen

Nu aktivera autentisering för webb-API med din första Azure AD-app. Mer information lär du dig [hur du konfigurerar din App tjänstprogram att använda Azure Active Directory-inloggningen](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. I den [Azure-portalen](https://portal.azure.com), hitta Web API-appen som du tidigare har publicerats i [skapa egna kopplingar från Web API: er](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. Under **inställningar**, Välj **autentisering / auktorisering**. 

   1. Under **App autentiseringen av tjänsten**, Välj **på**.
   2. För **åtgärd att vidta när begäran inte har autentiserats**väljer **logga in med Azure Active Directory**.
   3. För **autentiseringsproviders**väljer **Azure Active Directory**. 

   ![Välj ”autentisering / auktorisering”](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. På den **inställningarna för Azure Active Directory** sidan:

   1. Under **hanteringsläge**, Välj **Express**.

   2. Nu välja Azure AD-app som Web API-app använder för autentisering. 
   Välj **Välj befintligt AD-program** > **Azure AD-App**.

   3. Under **Azure AD-program**, Välj den Azure AD-app som du skapade tidigare för Web API-app. 
   
   4. Välj **OK** tills du kommer tillbaka till den **autentisering / auktorisering** sidan.

   Exempel:

   ![Välj den Azure AD-app som representerar Web API-appen](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. På den **autentisering / auktorisering** väljer **spara**.

   ![Spara inställningarna för autentisering](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Web API-appen kan nu använda Azure AD för autentisering.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Konfigurera Azure AD-autentisering i Web API OpenAPI fil

Öppna filen OpenAPI för Web API-app så att du kan lägga till den `securityDefintions` objektet och Azure AD authentication under den `host` egenskapen. Här är ett exempel som visar den `host` egenskapen och `securityDefinitions` objekt:

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
Nu är du redo att skapa och registrera ditt anpassad koppling.

## <a name="next-steps"></a>Nästa steg

* [Registrera din anslutningsapp](../logic-apps/logic-apps-custom-connector-register.md)
* [Anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)
