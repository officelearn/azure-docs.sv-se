---
title: Lägga till autentisering för att skydda anrop till anpassade API:er
description: Konfigurera autentisering för att förbättra säkerheten för anrop till anpassade API:er från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656241"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Öka säkerheten för samtal till anpassade API:er från Azure Logic Apps

För att förbättra säkerheten för samtal till dina API:er kan du konfigurera Azure Active Directory (Azure AD) autentisering via Azure-portalen så att du inte behöver uppdatera koden. Eller så kan du kräva och ställa in autentisering via API-koden.

## <a name="authentication-options-for-your-api"></a>Autentiseringsalternativ för ditt API

Du kan förbättra säkerheten för samtal till ditt anpassade API på följande sätt:

* [Inga kodändringar:](#no-code)Skydda ditt API med [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) via Azure-portalen, så du behöver inte uppdatera koden eller distribuera om ditt API.

  > [!NOTE]
  > Som standard ger Azure AD-autentisering som du aktiverar i Azure-portalen inte detaljerad auktorisering. Den här autentiseringen låser till exempel ditt API till bara en viss klient, inte för en viss användare eller app. 

* [Uppdatera API:s kod:](#update-code)Skydda ditt API genom att tillämpa [certifikatautentisering,](#certificate) [grundläggande autentisering](#basic)eller [Azure AD-autentisering](#azure-ad-code) via kod.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autentisera anrop till ditt API utan att ändra koden

Här är de allmänna stegen för den här metoden:

1. Skapa två Azure Active Directory -programidentiteter (Azure AD): en för logikappen och en för din webbapp (eller API-app).

2. Om du vill autentisera anrop till ditt API använder du autentiseringsuppgifterna (klient-ID och hemlighet) för tjänsthuvudhuvudet som är associerat med Azure AD-programidentiteten för logikappen.

3. Inkludera program-ID:na i logikappdefinitionen.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Del 1: Skapa en Azure AD-programidentitet för logikappen

Din logikapp använder den här Azure AD-programidentiteten för att autentisera mot Azure AD. Du behöver bara ställa in den här identiteten en gång för din katalog. Du kan till exempel välja att använda samma identitet för alla logikappar, även om du kan skapa unika identiteter för varje logikapp. Du kan ställa in dessa identiteter i Azure-portalen eller använda [PowerShell](#powershell).

**Skapa programidentiteten för logikappen i Azure-portalen**

1. Välj Azure Active **Directory**i [Azure-portalen](https://portal.azure.com "https://portal.azure.com"). 

2. Bekräfta att du är i samma katalog som webbappen eller API-appen.

   > [!TIP]
   > Om du vill byta kataloger väljer du din profil och väljer en annan katalog. Du kan också välja Katalog över > **översiktsväxlar**. **Overview**

3. Välj **Appregistreringar** > **Ny programregistrering**under **Hantera**på katalogmenyn.

   > [!TIP]
   > Som standard visar listan över appregistreringar alla appregistreringar i katalogen. Om du bara vill visa dina appregistreringar väljer du **Mina appar**bredvid sökrutan . 

   ![Skapa ny appregistrering](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Ge din programidentitet ett namn, lämna **programtyp** inställd på **Webbapp / API**, ge en unik sträng formaterad som en domän för **sign-on URL**och välj **Skapa**.

   ![Ange namn och inloggnings-URL för programidentitet](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Programidentiteten som du skapade för logikappen visas nu i listan över appregistreringar.

   ![Programidentitet för logikappen](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Välj din nya programidentitet i listan över appregistreringar. Kopiera och spara **program-ID:et** som ska användas som "klient-ID" för logikappen i del 3.

   ![Kopiera och spara program-ID för logikapp](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Om programidentitetsinställningarna inte visas väljer du **Inställningar** eller **Alla inställningar**.

7. Under **API Access**väljer du **Nycklar**. Under **Beskrivning**anger du ett namn på nyckeln. Under **Upphör att gälla**väljer du en varaktighet för nyckeln.

   Nyckeln som du skapar fungerar som programidentitetens "hemliga" eller lösenord för logikappen.

   ![Skapa nyckel för logikappidentitet](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Välj **Spara**i verktygsfältet . Under **Värde**visas nu nyckeln. 
**Se till att kopiera och spara nyckeln** för senare användning eftersom nyckeln är dold när du lämnar sidan **Nycklar.**

   När du konfigurerar logikappen i del 3 anger du den här nyckeln som "hemlig" eller lösenord.

   ![Kopiera och spara tangenten till senare](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Skapa programidentiteten för logikappen i PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan utföra den här uppgiften via Azure Resource Manager med PowerShell. I PowerShell kör du följande kommandon:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Ange ett lösenord och tryck på Retur.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Se till att kopiera **klient-ID** (GUID för din Azure AD-klientorganisation), **program-ID**och det lösenord som du använde.

Mer information finns i hur du [skapar ett tjänsthuvudnamn med PowerShell för att komma åt resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Del 2: Skapa en Azure AD-programidentitet för din webbapp eller API-app

Om din webbapp eller API-app redan har distribuerats kan du aktivera autentisering och skapa programidentiteten i Azure-portalen. Annars kan du [aktivera autentisering när du distribuerar med en Azure Resource Manager-mall](#authen-deploy). 

**Skapa programidentiteten och aktivera autentisering i Azure-portalen för distribuerade appar**

1. Leta reda på och välj webbapp eller API-app i [Azure-portalen.](https://portal.azure.com "https://portal.azure.com") 

2. Under **Inställningar**väljer du **Autentisering/auktorisering**. Aktivera **autentisering under App Service-autentisering** **.** Under **Autentiseringsleverantörer**väljer du **Azure Active Directory**.

   ![Aktivera autentisering](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Nu skapa en programidentitet för din webbapp eller API-app som visas här. På sidan **Azure Active Directory-inställningar** ställer du in **hanteringsläge** till **Express**. Välj **Skapa ny AD-app**. Ge programidentiteten ett namn och välj **OK**. 

   ![Skapa programidentitet för din webbapp eller API-app](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. På sidan **Autentisering/auktorisering** väljer du **Spara**.

Nu måste du hitta klient-ID och klient-ID för programidentiteten som är associerad med din webbapp eller API-app. Du använder dessa ID:er i del 3. Så fortsätt med de här stegen för Azure-portalen.

**Hitta programidentitetens klient-ID och klient-ID för din webbapp eller API-app i Azure-portalen**

1. Under **Autentiseringsleverantörer**väljer du **Azure Active Directory**. 

   ![Välj ”Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. På sidan **Azure Active Directory-inställningar** ställer du in **hanteringsläge** till **Avancerat**.

3. Kopiera **klient-ID**och spara det GUID för användning i del 3.

   > [!TIP] 
   > Om **klient-ID** och **utfärdar-url** inte visas kan du prova att uppdatera Azure-portalen och upprepa steg 1.

4. Under **Utfärdare Url**, kopiera och spara bara GUID för del 3. Du kan också använda det här GUID-programmet i distributionsmallen för webbappen eller API-appen om det behövs.

   Det här GUID:t är din specifika klientens GUID ("klient-ID") och ska visas i den här URL:en:`https://sts.windows.net/{GUID}`

5. Stäng sidan **Azure Active Directory-inställningar** utan att spara ändringarna.

<a name="authen-deploy"></a>

**Aktivera autentisering när du distribuerar med en Azure Resource Manager-mall**

Du måste fortfarande skapa en Azure AD-programidentitet för din webbapp eller API-app som skiljer sig från appidentiteten för logikappen. Om du vill skapa programidentiteten följer du föregående steg i del 2 för Azure-portalen. 

Du kan också följa stegen i del 1, men se till att `https://{URL}` använda webbappens eller API-appens faktiska för **inloggnings-URL** och **App-ID URI**. Från dessa steg måste du spara både klient-ID och klient-ID för användning i appens distributionsmall och även för del 3.

> [!NOTE]
> När du skapar Azure AD-programidentiteten för din webbapp eller API-app måste du använda Azure-portalen, inte PowerShell. PowerShell-kommandotuppsättningen ställer inte in de behörigheter som krävs för att logga in användare på en webbplats.

När du har hämtat klient-ID: t och klient-ID:n ska du inkludera dessa ID:n som en underresurs för din webbapp eller API-app i distributionsmallen:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Om du vill distribuera en tom webbapp och en logikapp automatiskt tillsammans med Azure Active Directory-autentisering [kan du visa den fullständiga mallen här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)eller klicka på Distribuera till **Azure** här:

[![Distribuera till Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Del 3: Fyll i avsnittet Auktorisering i logikappen

Den tidigare mallen har redan det här auktoriseringsavsnittet inställt, men om du redigerar logikappen direkt måste du inkludera hela auktoriseringsavsnittet.

Öppna logikappdefinitionen i kodvyn, **HTTP** gå till HTTP-åtgärdsdefinitionen, leta reda på avsnittet **Auktorisering** och inkludera följande egenskaper:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| tenant | Ja | GUID för Azure AD-klienten | 
| Publik | Ja | GUID för målresursen som du vill komma åt, som är klient-ID från programidentiteten för din webbapp eller API-app | 
| ClientID | Ja | GUID för klienten som begär åtkomst, vilket är klient-ID från programidentiteten för logikappen | 
| hemlighet | Ja | Nyckeln eller lösenordet från programidentiteten för klienten som begär åtkomsttoken | 
| typ | Ja | Autentiseringstypen. För ActiveDirectoryOAuth-autentisering `ActiveDirectoryOAuth`är värdet . | 
|||| 

Ett exempel:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Säkra API-anrop via kod

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Certifikatsautentisering

Om du vill validera inkommande begäranden från logikappen till din webbapp eller API-app kan du använda klientcertifikat. Om du vill konfigurera koden får du lära dig hur du [konfigurerar TLS-ömsesidig autentisering](../app-service/app-service-web-configure-tls-mutual-auth.md).

I avsnittet **Auktorisering** tar du med följande egenskaper:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `type` | Ja | Autentiseringstypen. För TLS/SSL-klientcertifikat måste värdet `ClientCertificate`vara . |
| `password` | Inga | Lösenordet för åtkomst till klientcertifikatet (PFX-fil) |
| `pfx` | Ja | Det base64-kodade innehållet i klientcertifikatet (PFX-fil) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Grundläggande autentisering

Om du vill validera inkommande begäranden från logikappen till din webbapp eller API-app kan du använda grundläggande autentisering, till exempel ett användarnamn och lösenord. Grundläggande autentisering är ett vanligt mönster och du kan använda den här autentiseringen på alla språk som används för att skapa din webbapp eller API-app.

I avsnittet **Auktorisering** tar du med följande egenskaper:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| typ | Ja | Den autentiseringstyp som du vill använda. För grundläggande autentisering måste `Basic`värdet vara . | 
| användarnamn | Ja | Användarnamnet som du vill använda för autentisering | 
| password | Ja | Lösenordet som du vill använda för autentisering | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory-autentisering via kod

Som standard ger Azure AD-autentisering som du aktiverar i Azure-portalen inte detaljerad auktorisering. Den här autentiseringen låser till exempel ditt API till bara en viss klient, inte för en viss användare eller app. 

Om du vill begränsa API-åtkomsten till logikappen via kod extraherar du huvudet som har JSON-webbtoken (JWT). Kontrollera den som ringers identitet och avvisa begäranden som inte matchar.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Nästa steg

* [Distribuera och anropa anpassade API:er från logikapparbetsflöden](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
