---
title: 'Lägg till autentisering i anpassade API: er – Azure Logic Apps | Microsoft Docs'
description: 'Konfigurera autentisering för att anropa anpassade API: er från Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: d83a27d87ffadd15a27196a11ae3f69d84232efa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719614"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Säkra anrop till anpassade API: er från Azure Logic Apps

Om du vill skydda anrop till API: er, kan du konfigurera Azure Active Directory (Azure AD)-autentisering via Azure portal så att du inte behöver uppdatera koden. Eller så kan du kräva och ställa in autentisering via API-koden.

## <a name="authentication-options-for-your-api"></a>Alternativ för autentisering för ditt API

Du kan skydda anrop till ditt anpassade API på följande sätt:

* [Inga kodändringar alls](#no-code): Skydda ditt API med [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) via Azure-portalen, så du behöver uppdatera koden eller distribuera om ditt API.

  > [!NOTE]
  > Som standard ger inte Azure AD-autentisering som du aktiverar i Azure portal detaljerad behörighet. Till exempel låser den här autentiseringen ditt API till bara en specifik klient, inte till en specifik användare eller app. 

* [Uppdatera API-koden](#update-code): Skydda ditt API genom att se [certifikatautentisering](#certificate), [grundläggande autentisering](#basic), eller [Azure AD-autentisering](#azure-ad-code) via kod.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autentisera dina API-anrop utan att ändra koden

Här följer de allmänna stegen för den här metoden:

1. Skapa två Azure Active Directory (Azure AD) application identiteter: en för din logikapp och en för din webbapp (eller API-app).

2. För att autentisera dina API-anrop, använda autentiseringsuppgifter (klient-ID och hemlighet) för tjänstens huvudnamn som är associerat med Azure AD-identitet för din logikapp för programmet.

3. Inkludera program-ID: N i din logikapp-definitioner.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Del 1: Skapa en Azure AD application identitet för din logikapp

Logikappen använder den här identiteten för Azure AD-program för att autentisera mot Azure AD. Du behöver bara konfigurera den här identiteten en gång för din katalog. Exempelvis kan du välja att använda samma identitet för dina logikappar trots att du kan skapa unika identiteter för varje logikapp. Du kan konfigurera dessa identiteter i Azure-portalen eller använda [PowerShell](#powershell).

**Skapa programidentitet för din logikapp i Azure portal**

1. I den [Azure-portalen](https://portal.azure.com "https://portal.azure.com"), Välj **Azure Active Directory**. 

2. Bekräfta att du befinner dig i samma katalog som din webbapp eller API-app.

   > [!TIP]
   > Om du vill växla kataloger, Välj din profil och välj en annan katalog. Eller välj **översikt** > **växla katalog**.

3. På katalogmenyn, under **hantera**, Välj **appregistreringar** > **ny programregistrering**.

   > [!TIP]
   > Som standard visar listan över registreringar alla app-registreringar i din katalog. Om du vill visa endast dina appregistreringar bredvid sökrutan, Välj **Mina appar**. 

   ![Skapa ny appregistrering](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Namnge din Programidentitet, lämna **programtyp** inställd **webbapp / API**, ange ett unikt sträng formaterad som en domän för **inloggnings-URL**, och välj  **Skapa**.

   ![Ange namn och inloggnings-URL för Programidentitet](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Programidentitet som du skapade för din logikapp nu visas i listan över registreringar.

   ![Programidentitet för din logikapp](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Välj din nya applikationsidentitet i listan med app-registreringar. Kopiera och spara den **program-ID** ska användas som ”klient-ID” för din logikapp i del 3.

   ![Kopiera och spara program-ID för logikapp](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Om inställningarna för ditt program inte visas väljer du **inställningar** eller **alla inställningar**.

7. Under **API-åtkomst**, Välj **nycklar**. Under **beskrivning**, ange ett namn för din nyckel. Under **förfaller**, Välj varaktighet för din nyckel.

   Den nyckel som du skapar fungerar som programidentiteten ”hemligheten” eller ett lösenord för din logikapp.

   ![Skapa nyckel för logic app-identitet](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. I verktygsfältet, välja **spara**. Under **värdet**, din nyckel visas nu. 
**Se till att kopiera och spara nyckeln** för senare användning eftersom nyckeln döljs när du lämnar den **nycklar** sidan.

   När du konfigurerar din logikapp i del 3, anger du den här nyckeln som ”hemlighet” eller lösenord.

   ![Kopiera och spara nyckeln för senare](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Skapa programidentitet för din logikapp i PowerShell**

Du kan utföra den här uppgiften via Azure Resource Manager med PowerShell. Kör följande kommandon i PowerShell:

1. `Add-AzureRmAccount`

2. `$SecurePassword = Read-Host -AsSecureString` (Ange ett lösenord och tryck på RETUR)

3. `New-AzureRmADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

4. Kom ihåg att kopiera den **klient-ID** (GUID för Azure AD-klienten), den **program-ID**, och det lösenord som du använde.

Mer information lär du dig hur du [skapa tjänstens huvudnamn med PowerShell för att komma åt resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Del 2: Skapa en Azure AD application identitet för din webbapp eller API-app

Om din webbapp eller API-app redan har distribuerats kan du aktivera autentisering och skapar programidentiteten i Azure-portalen. Du kan [aktivera autentisering när du distribuerar med en Azure Resource Manager-mall](#authen-deploy). 

**Skapa programidentiteten och aktivera autentisering i Azure-portalen för distribuerade appar**

1. I den [Azure-portalen](https://portal.azure.com "https://portal.azure.com"), hitta och välj din webbapp eller API-app. 

2. Under **inställningar**, Välj **autentisering/auktorisering**. Under **Apptjänstautentisering**, aktivera autentisering **på**. Under **autentiseringsproviders**, Välj **Azure Active Directory**.

   ![Aktivera autentisering](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Skapa nu en programidentitet för din webbapp eller API-app som visas här. På den **Azure Active Directory-inställningar** ställer du in **hanteringsläge** till **Express**. Välj **Skapa nytt AD-App**. Namnge din Programidentitet och välj **OK**. 

   ![Skapa programmet identitet för din webbapp eller API-app](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. På sidan **Autentisering/auktorisering** väljer du **Spara**.

Nu måste du söka efter klient-ID och klient-ID för applikationsidentitet som är associerat med din webbapp eller API-app. Du kan använda dessa ID: N i del 3. Så fortsätter du med de här stegen för Azure-portalen.

**Hitta programmet identitet klient-ID och klient-ID för din webbapp eller API-app i Azure portal**

1. Under **autentiseringsproviders**, Välj **Azure Active Directory**. 

   ![Välj ”Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. På den **Azure Active Directory-inställningar** ställer du in **hanteringsläge** till **Avancerat**.

3. Kopiera den **klient-ID**, och spara denna GUID för användning i del 3.

   > [!TIP] 
   > Om **klient-ID** och **utfärdar-Url** inte visas, försök att uppdatera Azure-portalen och upprepa steg 1.

4. Under **utfärdar-Url**, kopiera och spara bara GUID för en del 3. Du kan också använda detta GUID i web Apps eller API-appens Distributionsmall, om det behövs.

   Detta GUID är din klient-GUID (”klient-ID”) och visas i den här URL: en: `https://sts.windows.net/{GUID}`

5. Utan att spara dina ändringar att Stäng den **Azure Active Directory-inställningar** sidan.

<a name="authen-deploy"></a>

**Aktivera autentisering när du distribuerar med en Azure Resource Manager-mall**

Du måste fortfarande skapa en Azure AD application identitet för din webbapp eller API-app som skiljer sig från appen identiteten för din logikapp. Följ de föregående stegen i del 2 för Azure-portalen för att skapa programidentiteten. 

Du kan också följa stegen i del 1, men se till att använda din webbapp eller API-appens faktiska `https://{URL}` för **inloggnings-URL** och **Appidentitets-URI**. Du måste spara både klient-ID och klient-ID för användning i din app Distributionsmall och även för del 3 från de här stegen.

> [!NOTE]
> När du skapar Azure AD-identitet för din webbapp eller API-app för programmet, måste du använda Azure-portalen inte PowerShell. PowerShell-kommandot Konfigurera inte behörigheterna som krävs för att logga in användare på en webbplats.

När du har hämtat klient-ID och klient-ID kan du inkludera dessa ID: N som en underresurs för din webbapp eller API-app i din Distributionsmall för:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Så här distribuerar automatiskt en tom webbapp och en logikapp tillsammans med Azure Active Directory-autentisering, [visa den fullständiga mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), eller klicka på **distribuera till Azure** här:

[![Distribuera till Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Del 3: Fyll i avsnittet om auktorisering i din logikapp

Den tidigare mallen har redan det här avsnittet för auktorisering som ställer in, men om du använder direkt logikappen, måste du inkludera avsnittet fullständig behörighet.

Öppna din logikapp-definitioner i kodvyn går du till den **HTTP** delen åtgärd hitta den **auktorisering** avsnittet och inkludera den här raden:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Element | Krävs | Beskrivning | 
| ------- | -------- | ----------- | 
| klient | Ja | GUID för Azure AD-klient | 
| Målgrupp | Ja | GUID för vilken resurs som du vill komma åt, vilket är klient-ID från programidentitet för din webbapp eller API-app | 
| ClientId | Ja | GUID för klienten som begärde åtkomst, vilket är klient-ID från programidentitet för din logikapp | 
| hemlighet | Ja | Nyckeln eller lösenordet från programidentitet för den klient som begär åtkomst-token | 
| typ | Ja | Autentiseringstypen. Värdet är för ActiveDirectoryOAuth autentisering, `ActiveDirectoryOAuth`. | 
|||| 

Exempel:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Säkra API-anrop med hjälp av kod

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Certifikatautentisering

Du kan använda klientcertifikat för att verifiera inkommande begäranden från logikappen till din webbapp eller API-app. Om du vill konfigurera din kod, lär du dig [konfigurera TLS ömsesidig autentisering](../app-service/app-service-web-configure-tls-mutual-auth.md).

I den **auktorisering** avsnittet, innehåller den här raden: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Element | Krävs | Beskrivning | 
| ------- | -------- | ----------- | 
| typ | Ja | Autentiseringstypen. För SSL-klientcertifikat, värdet måste vara `ClientCertificate`. | 
| lösenord | Ja | Lösenord för att komma åt klientcertifikatet (PFX-fil) | 
| pfx | Ja | Base64-kodad innehållet i klientcertifikatet (PFX-fil) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Grundläggande autentisering

Du kan använda grundläggande autentisering, till exempel ett användarnamn och lösenord för att verifiera inkommande begäranden från logikappen till din webbapp eller API-app. Grundläggande autentisering är ett vanligt mönster och du kan använda den här autentiseringen på valfritt språk som används för att skapa din webbapp eller API-app.

I den **auktorisering** avsnittet, innehåller den här raden:

`{"type": "basic", "username": "username", "password": "password"}`.

| Element | Krävs | Beskrivning | 
| ------- | -------- | ----------- | 
| typ | Ja | Den autentiseringstyp som du vill använda. För grundläggande autentisering, värdet måste vara `Basic`. | 
| användarnamn | Ja | Det användarnamn som du vill använda för autentisering | 
| lösenord | Ja | Det lösenord som du vill använda för autentisering | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory-autentisering via kod

Som standard ger inte Azure AD-autentisering som du aktiverar i Azure portal detaljerad behörighet. Till exempel låser den här autentiseringen ditt API till bara en specifik klient, inte till en specifik användare eller app. 

Extrahera rubriken med JSON-webbtoken (JWT) för att begränsa API-åtkomst till din logikapp med hjälp av kod. Kontrollera Anroparens identitet och avvisa förfrågningar som inte matchar.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Nästa steg

* [Distribuera och anropa anpassade API: er från logic app-arbetsflöden](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
