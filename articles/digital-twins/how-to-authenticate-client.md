---
title: Autentisera ett klient program
titleSuffix: Azure Digital Twins
description: Se så här autentiserar du ett klient program mot tjänsten Azure Digitals dubbla.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8390ed8777c2c15940bef5838890e49396404052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613273"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Autentisera ett klient program med Azure Digitals dubbla

När du har [skapat en Azure Digitals-instans](how-to-set-up-instance.md)kan du skapa ett klient program som ska användas för att interagera med instansen. När du har konfigurerat ett start klient projekt visar den här artikeln hur du korrekt autentiserar klient programmet med Azure Digitals-instansen.

Detta görs i två steg:
1. Skapa en app-registrering
2. Skriv kod för autentisering i ett klient program

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Skapa en app-registrering

Om du vill autentisera mot digitala Azure-delningar från ett klient program måste du konfigurera en **app-registrering** i [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](how-to-use-apis-sdks.md). Klient programmet autentiseras mot appens registrering och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.

Om du vill skapa en app-registrering måste du ange resurs-ID: n för Azure Digitals dubbla API: er och bas linje behörigheterna till API: et. Öppna en ny fil i arbets katalogen och ange följande JSON-kodfragment för att konfigurera den här informationen: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Spara filen som *manifest. JSON*.

> [!NOTE] 
> Det finns vissa platser där en "läsvänlig", läsbar sträng `https://digitaltwins.azure.net` kan användas för resurs-ID: t för Azure Digital tillsammans i stället för GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Till exempel använder många exempel i den här dokumentationen autentisering med MSAL-biblioteket och den egna strängen kan användas för det. Men under det här steget för att skapa appens registrering krävs GUID-formatet för ID: t som visas ovan. 

I Cloud Shell-fönstret klickar du på ikonen Ladda upp/ladda ned filer och väljer överför.

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Cloud Shell fönster som visar val av överförings alternativ":::
Navigera till *manifest. JSON* som du precis skapade och tryck på "öppna".

Kör sedan följande kommando för att skapa en app-registrering (ersätter plats hållare vid behov):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Utdata från det här kommandot ser ut ungefär så här.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Ny AAD-app-registrering":::

När du har skapat registreringen av appen följer du [den här länken](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) för att gå till översikts sidan för AAD-appen registrering i Azure Portal.

I den här översikten väljer du den app-registrering som du nyss skapade i listan. Då öppnas informationen på en sida som den här:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: ID för autentisering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . Du kommer att använda dessa värden senare för att autentisera en app mot Azures digitala dubbla API: er.

> [!NOTE]
> Beroende på ditt scenario kan du behöva göra ytterligare ändringar i appens registrering. Här följer några vanliga krav som du kan behöva uppfylla:
> * Aktivera offentlig klient åtkomst
> * Ange vissa svars-URL: er för webb-och skriv bords åtkomst
> * Tillåt för implicita OAuth2-autentiserings flöden
> * Om din Azure-prenumeration har skapats med hjälp av en Microsoft-konto som Live, Xbox eller Hotmail, måste du ange *signInAudience* på App-registreringen för att stödja personliga konton.
> Det enklaste sättet att konfigurera dessa inställningar är att använda [Azure Portal](https://portal.azure.com/). Mer information om den här processen finns i [Registrera ett program med Microsoft Identity Platform](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Skriv kod för klientautentisering: .NET (C#) SDK

I det här avsnittet beskrivs den kod som du måste ta med i klient programmet för att kunna slutföra autentiseringsprocessen med hjälp av .NET (C#) SDK.
Azure Digitals med C# SDK är en del av Azure SDK för .NET. Den finns här: [Azure IoT Digital-klient bibliotek för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Krav

Om du inte redan har skapat ett program för klient programmet för Start programmet, skapar du ett grundläggande .NET-projekt som du kan använda med den här självstudien.

Du måste inkludera följande paket i projektet för att kunna använda .NET SDK:
* `Azure.DigitalTwins.Core`(version `1.0.0-preview.2` )
* `Azure.Identity`

Beroende på vilka verktyg du väljer kan du göra det med Visual Studio Package Manager eller `dotnet` kommando rads verktyget. 

### <a name="authentication-and-client-creation-net"></a>Autentisering och klient skapande: .NET

Om du vill autentisera med .NET SDK använder du en av de metoder för autentisering som har definierats i [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -biblioteket.

Här är två som används ofta: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Den här metoden är avsedd för interaktiva program och kommer att hämta en webbläsare för autentisering.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Den här metoden fungerar bra i de fall där du behöver [hanterade identiteter (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), till exempel när du arbetar med Azure Functions. 

Du behöver också följande using-instruktioner:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Om du vill använda de interaktiva webb läsar autentiseringsuppgifterna för att skapa en autentiserad SDK-klient lägger du till följande kod:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Även om du kan placera klient-ID, klient-ID och instans-URL direkt i koden som visas ovan, är det en bra idé att låta koden hämta dessa värden från en konfigurations fil eller en miljö variabel i stället.

I en Azure-funktion kan du sedan använda autentiseringsuppgifterna för hanterad identitet som detta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Se [anvisningar: Konfigurera en Azure-funktion för att bearbeta data](how-to-create-azure-function.md) för ett mer komplett exempel som förklarar några av de viktiga konfigurations alternativen i kontexten för functions.

Om du vill använda autentisering i en funktion måste du också komma ihåg att:
* [Aktivera hanterad identitet](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Miljövariabler](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Tilldela behörigheter till Functions-appen som möjliggör åtkomst till digitala dubbla API: er. Se [anvisningar: Konfigurera en Azure-funktion för att bearbeta data](how-to-create-azure-function.md) för mer information.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Autentisering i en AutoRest-genererad SDK

Om du inte använder .NET kan du välja att skapa ett SDK-bibliotek på ett valfritt språk, enligt beskrivningen i [instruktion: skapa anpassade SDK: er för Azure Digitals med AutoRest](how-to-create-custom-sdks.md).

I det här avsnittet beskrivs hur du autentiserar i så fall.

### <a name="prerequisites"></a>Krav

I det här exemplet används en typescript-SDK som genereras med AutoRest. Därför kräver det också:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Minimal kod exempel för autentisering

Om du vill autentisera en .NET-app med Azure-tjänster kan du använda följande minimala kod i klient programmet.

Du behöver ditt *program (klient) ID* och *katalog (klient) ID* från tidigare, samt URL: en för din Azure Digital-instansen.

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet*för Azure Digital-instansen. Om du vill se värd namnet, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Observera att om koden ovan placerar klient-ID, klient-ID och instans-URL direkt i koden för enkelhetens skull är det en bra idé att låta koden hämta de här värdena från en konfigurations fil eller en miljö variabel i stället.

MSAL har många fler alternativ som du kan använda för att implementera saker som cachelagring och andra autentiserings flöden. Mer information om detta finns i [Översikt över Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur säkerheten fungerar i Azure Digitals:
* [Koncept: säkerhet för Azure Digitals dubbla lösningar](concepts-security.md)

Eller, nu när autentiseringen har kon figurer ATS, går du vidare till att skapa modeller i din instans:
* [Anvisningar: hantera en dubbel modell](how-to-manage-model.md)