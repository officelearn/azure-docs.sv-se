---
title: 'Självstudie: Azure SignalR Service-autentisering med Azure Functions'
description: I den här självstudien har du lärt dig att autentisera Azure SignalR Service-klienter
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 0cd0dcaf200b1248204efc2d2c0011a94d3c41d3
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720976"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Självstudie: Azure SignalR Service-autentisering med Azure Functions

En stegvis självstudiekurs som beskriver hur du skapar ett chattrum med autentisering och privata meddelanden med Azure Functions, App Service-autentisering och SignalR Service.

## <a name="introduction"></a>Introduktion

### <a name="technologies-used"></a>Tekniker som används

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Serverdels-API för att autentisera användare och skicka chattmeddelanden
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Skicka nya meddelanden till anslutna chattklienter
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Lagra den statiska webbplatsen för chattklientens användargränssnitt

### <a name="prerequisites"></a>Nödvändiga komponenter

Följande programvara krävs för den här självstudien.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (version 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (version 2.x, krävs för Functions-tillägg)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (version 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) med följande tillägg
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – Arbeta med Azure Functions i VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – Betjäna webbsidor lokalt för testning

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Gå till [Azure-portalen](https://portal.azure.com/) och logga in med dina autentiseringsuppgifter.

## <a name="create-an-azure-signalr-service-instance"></a>Skapa en Azure SignalR Service-instans

Du kan skapa och testa Azure Functions-appen lokalt. Appen använder en SignalR Service-instans i Azure som måste skapas i förväg.

1. Klicka på knappen **Skapa en resurs** (**+**) för att skapa en ny resurs i Azure.

1. Sök efter och välj **SignalR Service**. Klicka på **Skapa**.

    ![Ny SignalR-tjänst](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Ange följande information.

    | Namn | Värde |
    |---|---|
    | Resursnamn | Ett unikt namn för SignalR Service-instansen |
    | Resursgrupp | Skapa en ny resursgrupp |
    | Plats | Välj en plats nära dig |
    | Prisnivå | Kostnadsfri |

1. Klicka på **Skapa**.

## <a name="initialize-the-function-app"></a>Initiera funktionsappen

### <a name="create-a-new-azure-functions-project"></a>Skapa ett nytt Azure Functions-projekt

1. Använd `File > Open Folder` på menyn i ett nytt VS Code-fönster för att skapa och öppna en tom mapp på lämplig plats. Det här är den primära projektmappen för programmet som du skapar.

1. Använd Azure Functions-tilläggen i VS Code för att initiera en funktionsapp i den primära projektmappen.
    1. Öppna kommandopaletten i VS Code genom att välja **Visa > Kommandopalett** från menyn (genväg `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
    1. Sök efter kommandot **Azure Functions: Create New Project** (Skapa nytt projekt) och välj det.
    1. Den primära projektmappen bör visas. Välj den (eller använd ”Bläddra” för att leta upp den).
    1. När du uppmanas att välja ett språk väljer du **JavaScript**.

    ![Skapa en funktionsapp](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Installera funktionsapptillägg

I den här självstudien används Azure Functions-bindningar för att interagera med Azure SignalR Service. Som de flesta andra bindningar är SignalR Service-bindningarna tillgängliga som tillägg som måste installeras med hjälp av Azure Functions Core Tools CLI innan de kan användas.

1. Öppna en terminal i VS Code genom att välja **Visa > Integrated Terminal** (Integrerad terminal) från menyn (Ctrl-\`).

1. Kontrollera att den primära projektmappen är den aktuella katalogen.

1. Installera SignalR Service-funktionsapptillägget.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Konfigurera programinställningar

När du kör och felsöker Azure Functions-körningsmiljön lokalt läser Azure Functions programinställningar från **local.settings.json**. Uppdatera den här filen med anslutningssträngen för den SignalR Service-instans som du skapade tidigare.

1. Välj **local.settings.json** i Explorer-fönstret i VS Code för att öppna filen.

1. Ersätt filens innehåll med följande.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Ange anslutningssträngen för Azure SignalR Service i en inställning med namnet `AzureSignalRConnectionString`. Hämta värdet från sidan **Nycklar** i Azure SignalR Service-resursen på Azure-portalen. Du kan använda antingen den primära eller den sekundära anslutningssträngen.
    * Inställningen `WEBSITE_NODE_DEFAULT_VERSION` används inte lokalt, men krävs när du distribuerar till Azure.
    * Avsnittet `Host` konfigurerar porten och CORS-inställningarna för den lokala Functions-värden (den här inställningen har ingen effekt när du kör i Azure).

    ![Hämta SignalR Service-nyckeln](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Spara filen.

    ![Uppdatera de lokala inställningarna](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Skapa en funktion för att autentisera användare till SignalR Service

När chattappen öppnas i webbläsaren krävs giltiga autentiseringsuppgifter för att ansluta till Azure SignalR Service. Du skapar en HTTP-utlöst funktion med namnet *SignalRInfo* i funktionsappen för att returnera den här anslutningsinformationen.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Create Function** (Skapa funktion).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mappen för funktionsappen | Välj den primära projektmappen |
    | Mall | HTTP-utlösare |
    | Namn | SignalRInfo |
    | Auktorisationsnivå | Anonym |

    En mapp med namnet **SignalRInfo** skapas som innehåller den nya funktionen.

1. Öppna **SignalRInfo/function.json** för att konfigurera bindningar för funktionen. Ändra innehållet i filen till följande. En indatabindning läggs till som genererar giltiga autentiseringsuppgifter för en klient så att den kan ansluta till en Azure SignalR Service-hubb med namnet `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    Egenskapen `userId` i bindningen `signalRConnectionInfo` används för att skapa en autentiserad SignalR Service-anslutning. Lämna egenskapen tom för lokal utveckling. Du ska använda den när funktionsappen har distribuerats till Azure.

1. Öppna **SignalRInfo/index.js** för att visa själva innehållet i funktionen. Ändra innehållet i filen till följande.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Den här funktionen hämtar SignalR-anslutningsinformationen från indatabindningen och returnerar den till klienten i HTTP-svarstexten.

## <a name="create-a-function-to-send-chat-messages"></a>Skapa en funktion för att skicka chattmeddelanden

Webbappen kräver också ett HTTP-API för att skicka chattmeddelanden. Du ska skapa en HTTP-utlöst funktion med namnet *SendMessage* som skickar meddelanden till alla anslutna klienter som använder SignalR Service.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Create Function** (Skapa funktion).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mappen för funktionsappen | Välj den primära projektmappen |
    | Mall | HTTP-utlösare |
    | Namn | SendMessage |
    | Auktorisationsnivå | Anonym |

    En mapp med namnet **SendMessage** skapas som innehåller den nya funktionen.

1. Öppna **SendMessage/function.json** för att konfigurera bindningar för funktionen. Ändra innehållet i filen till följande.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Två ändringar görs i den ursprungliga funktionen:
    * Ändrar vägen till `messages` och begränsar HTTP-utlösaren till **POST** HTTP-metoden.
    * Lägger till en SignalR Service-utdatabindning som skickar meddelanden till alla klienter som är anslutna till en SignalR Service-hubb med namnet `chat`.

1. Spara filen.

1. Öppna **SendMessage/index.js** för att visa själva innehållet i funktionen. Ändra innehållet i filen till följande.

    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```

    Den här funktionen hämtar innehållet i HTTP-begäran och skickar det till klienter som är anslutna till SignalR Service, och anropar en funktion med namnet `newMessage` på varje klient.

    Funktionen kan läsa avsändarens identitet och kan acceptera ett *mottagarvärde* i meddelandetexten så att ett meddelande kan skickas privat till en enskild användare. Vi kommer att använda dessa funktioner senare under kursen.

1. Spara filen.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Skapa och köra chattklientens webbaserade användargränssnitt

Chattprogrammets användargränssnitt är en enkel ensidesapplikation (SPA) som skapas med Vue JavaScript-ramverket. Det lagras separat från funktionsappen. Lokalt kör du webbgränssnittet med VS Code-tillägget Live Server.

1. Skapa en ny mapp i VS Code med namnet **content** i roten till den primära projektmappen.

1. Skapa en ny fil med namnet **index.html** i mappen **content**.

1. Kopiera och klistra in innehållet i **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Spara filen.

1. Tryck på **F5** för att köra funktionsappen lokalt och koppla en felsökare.

1. När filen **index.html** är öppen startar du Live Server genom att öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) och välja **Live Server: Open with Live Server** (Öppna med Live Server). Live Server öppnar programmet i en webbläsare.

1. Programmet öppnas. Ange ett meddelande i chattrutan och tryck på Retur. Uppdatera programmet för att se nya meddelanden. Eftersom ingen autentisering har konfigurerats skickas alla meddelanden som anonyma.

## <a name="deploy-to-azure-and-enable-authentication"></a>Distribuera till Azure och aktivera autentisering

Du har kört funktionsappen och chattprogrammet lokalt. Nu ska du distribuera dem till Azure och aktivera autentisering och privata meddelanden i programmet.

### <a name="log-into-azure-with-vs-code"></a>Logga in i Azure med VS Code

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure: Sign in** (Logga in).

1. Följ instruktionerna för att slutföra inloggningsprocessen i webbläsaren.

### <a name="configure-function-app-for-authentication"></a>Konfigurera funktionsappen för autentisering

Hittills fungerar chattappen anonymt. Du ska använda [App Service-autentisering](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) i Azure för att autentisera användaren. Användar-ID:t eller användarnamnet för den autentiserade användaren kan skickas till *SignalRConnectionInfo*-bindningen för att generera anslutningsinformation som autentiseras som användaren.

När ett meddelande skickas kan appen bestämma om det ska skickas till alla anslutna klienter, eller endast till de klienter som har autentiserats för en viss användare.

1. Öppna **SendMessage/function.json** i VS Code.

1. Infoga ett [bindningsuttryck](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns) i egenskapen *userId* för *SignalRConnectionInfo*-bindningen: `{headers.x-ms-client-principal-name}`. Detta anger värdet till användarnamnet för den autentiserade användaren. Attributet bör nu se ut så här.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Spara filen.

### <a name="deploy-function-app"></a>Distribuera funktionsappen

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) och välj **Azure Functions: Deploy to Function App** (Distribuera till funktionsapp).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Mapp som ska distribueras | Välj den primära projektmappen |
    | Prenumeration | Välj din prenumeration |
    | Funktionsapp | Välj **Create New Function App** (Skapa ny funktionsapp) |
    | Funktionsappens namn | Ange ett unikt namn |
    | Resursgrupp | Välj samma resursgrupp som SignalR Service-instansen |
    | Lagringskonto | Välj **Skapa nytt lagringskonto** |
    | Lagringskontots namn | Ange ett unikt namn (mellan 3 och 24 tecken, endast alfanumeriska) |
    | Plats | Välj en plats nära dig |

    En ny funktionsapp skapas i Azure och distributionen börjar. Vänta tills distributionen har slutförts.

### <a name="upload-function-app-local-settings"></a>Ladda upp lokala inställningar för funktionsappen

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Upload local settings** (Ladda upp lokala inställningar).

1. När du uppmanas att göra det anger du följande information.

    | Namn | Värde |
    |---|---|
    | Fil för lokala inställningar | local.settings.json |
    | Prenumeration | Välj din prenumeration |
    | Funktionsapp | Välj den tidigare distribuerade funktionsappen |
    | Funktionsappens namn | Ange ett unikt namn |

Lokala inställningar laddas upp till funktionsappen i Azure. Om du uppmanas att skriva över befintliga inställningar väljer du **Ja till allt**.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Aktivera CORS (Cross Origin Resource Sharing) för funktionsappen

Även om det finns en CORS-inställning i **local.settings.json** sprids den inte till funktionsappen i Azure. Du måste ställa in den separat.

1. Öppna kommandopaletten i VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Leta upp och välj kommandot **Azure Functions: Open in portal** (Öppna i portalen).

1. Välj prenumerationen och funktionsappens namn för att öppna funktionsappen på Azure-portalen.

1. På portalen som öppnades väljer du **CORS** under fliken **Plattformsfunktioner**.

    ![Leta upp CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Lägg till en post med värdet `*`.

1. Ta bort alla andra befintliga poster.

1. Klicka på **Spara** för att spara CORS-inställningarna.

    ![Konfigurera CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> I ett verkligt program är det säkrare att ange specifika CORS-poster för varje domän som kräver det i stället för att tillåta CORS på alla ursprung (`*`).

### <a name="update-the-web-app"></a>Uppdatera webbappen

1. Navigera till funktionsappens översiktssida på Azure-portalen.

1. Kopiera funktionsappens URL.

    ![Hämta URL:en](media/signalr-authenticate-azure-functions/signalr-get-url.png)

1. Öppna **index.html** i VS Code och ersätt värdet `apiBaseUrl` med funktionsappens URL.

1. Programmet kan konfigureras med autentisering med hjälp av Azure Active Directory, Facebook, Twitter, ett Microsoft-konto eller Google. Välj den autentiseringsprovider som du ska använda genom att ange värdet för `authProvider`.

1. Spara filen.

### <a name="deploy-the-web-application-to-blob-storage"></a>Distribuera webbprogrammet till bloblagring

Webbprogrammet ska hanteras av funktionen för statiska webbplatser i Azure Blob Storage.

1. Klicka på knappen **Ny** (**+**) för att skapa en ny Azure-resurs.

1. Under **Lagring** väljer du **Lagringskonto**.

1. Ange följande information.

    | Namn | Värde |
    |---|---|
    | Namn | Ett unikt namn för bloblagringskontot |
    | Typ av konto | StorageV2 (generell användning v2) |
    | Plats | Välj samma region som dina andra resurser |
    | Replikering | Lokalt redundant lagring (LRS) |
    | Prestanda | Standard |
    | Åtkomstnivå | Frekvent |
    | Resursgrupp | Välj samma resursgrupp som de andra resurserna i den här självstudien |

1. Klicka på **Skapa**.

    ![Skapa lagring](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. När lagringskontot har skapats kan du öppna det på Azure-portalen.

1. Välj **Statisk webbplats (förhandsversion)** i det vänstra navigeringsfönstret.

1. Välj **Aktivera**.

1. Ange `index.html` som **indexdokumentnamn**.

1. Klicka på **Spara**.

    ![Konfigurera statiska platser](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Klicka på länken **$web** på sidan för att öppna blobcontainern.

1. Klicka på **Överför** och ladda upp **index.html** i mappen **content**.

1. Gå tillbaka till sidan **Statisk webbplats**. Notera den **primära slutpunkten**. Det här är URL:en för webbprogrammet.

### <a name="enable-app-service-authentication"></a>Aktivera App Service-autentisering

App Service-autentisering har stöd för autentisering med Azure Active Directory, Facebook, Twitter, ett Microsoft-konto och Google.

1. När funktionsappen fortfarande är öppen på portalen letar du upp fliken **Plattformsfunktioner** och väljer **Autentisering/auktorisering**.

1. **Aktivera** App Service-autentisering.

1. I **Åtgärd att vidta när en begäran inte har autentiserats** väljer du ”Logga in med {autentiseringsprovidern som du valde tidigare}”.

1. I **Tillåtna externa omdirigeringswebbadresser** anger du URL:en för den primära webbslutpunkten för lagringskontot som du skrev ned tidigare.

1. Slutför konfigurationen genom att följa anvisningarna i dokumentationen för din valda inloggningsprovider.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Microsoft-konto](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="try-the-application"></a>Testa programmet

1. Gå till lagringskontots primära webbslutpunkt i en webbläsare.

1. Välj **Logga in** för att autentisera med din valda autentiseringsprovider.

1. Skicka offentliga meddelanden genom att skriva dem i den primära chattrutan.

1. Skicka privata meddelanden genom att klicka på ett användarnamn i chatthistoriken. Endast den valda mottagaren får dessa meddelanden.

Grattis! Du har distribuerat en serverlös realtidschattapp.

![Demo](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa resurserna som skapats i den här självstudien genom att ta bort resursgruppen via Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Azure Functions med Azure SignalR Service. Läs mer om hur du skapar serverlösa realtidsprogram med SignalR Service-bindningar för Azure Functions.

> [!div class="nextstepaction"]
> [Skapa realtidsappar med Azure Functions](signalr-overview-azure-functions.md)