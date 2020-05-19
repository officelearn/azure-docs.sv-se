---
title: Lägg till ett API i Azures statiska Web Apps med Azure Functions
description: Kom igång med Azures statiska Web Apps genom att lägga till ett Server lös API i din statiska webbapp med hjälp av Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598460"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Lägg till en API till för hands versionen av Azure statisk Web Apps med Azure Functions

Du kan lägga till Server lös API: er i Azures statiska Web Apps via integration med Azure Functions. Den här artikeln visar hur du lägger till och distribuerar ett API till en statisk Azure-Web Apps webbplats.

Information om hur du skyddar API-vägar finns i [guiden routning](routes.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free).
- [Visuell Studio-kod](https://code.visualstudio.com/)
- [Azure Functions tillägg](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) -tillägg.

## <a name="create-a-git-repository"></a>Skapa en git-lagringsplats 

Följande steg visar hur du skapar en ny lagrings plats och klonar filerna till din dator.

1. Navigera till https://github.com/staticwebdev/vanilla-basic/generate för att skapa en ny lagrings plats.
1. I rutan _databas namn_ anger du **My-vanilj-API**.
1. Klicka på **skapa lagrings plats från mall**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Skapa en ny lagrings plats från vanilj – Basic":::

När projektet har skapats kan du använda Visual Studio Code för att klona git-lagringsplatsen.

1. Tryck på **F1** för att öppna kommandot i kommando paletten.
1. Klistra in webb adressen i _git: Clone_ -prompten och tryck på **RETUR**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klona ett GitHub-projekt med Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Klona ett GitHub-projekt med Visual Studio Code":::


## <a name="create-your-local-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt. Senare publicerar du appen Functions till Azure.

1. I _mitt-vanilj-API-_ projektet skapar du en undermapp med namnet **API**.

   > [!NOTE]
   > Du kan ge den här mappen ett valfritt namn. I det här exemplet används `api` . 

2. Tryck på **F1** för att öppna kommando paletten
3. Skriv **Azure Functions: skapa nytt projekt...**
4. Tryck på **RETUR**
5. Välj **Bläddra**
6. Välj mappen **API** som katalog för projekt arbets ytan
7. Välj **Välj**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

8. Ange följande information i prompten:

    - _Välj ett språk för ditt funktions projekt_: Välj **Java Script**
    - _Välj en mall för projektets första funktion_: Välj **http-utlösare**
    - _Ange ett funktions namn_: Skriv **GetMessage**
    - _Autentiseringsnivå_: Välj **Anonym**, vilket gör att vem som helst kan anropa funktions slut punkten.
        - Mer information om auktoriseringsregler finns i [Authorization Keys](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare.
    - Du kan visa de lokala projektfilerna i Visual Studio Codes Explorer-fönstret.
    - Mer information om filer som skapas finns i [genererade projektfiler](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Din app bör nu ha en projekt struktur som liknar det här exemplet.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Uppdatera sedan `GetMessage` funktionen under _API/GetMessage/index. js_ med följande kod.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Uppdatera `GetMessage` konfigurationen under `api/GetMessage/function.json` med följande inställningar.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
Med ovanstående inställningar är API-slutpunkten:

- Utlöst med en HTTP-begäran görs till funktionen
- Tillgänglig för alla begär Anden oavsett autentiserings status
- Exponeras via _/API/Message_ -vägen

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Kör funktionen genom att trycka på **F5** för att starta appen Functions och kärn verktygets utdata visas på panelen _Terminal_ .

2. Om Azure Functions Core Tools inte redan är installerat väljer du **Installera** vid prompten.

    När kärn verktygen är installerade startar din app på panelen _Terminal_ . Som en del av utdata kan du se URL-slutpunkten för din HTTP-utlöst funktion som körs lokalt.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

3. Med kärn verktyg som kör, navigerar du till följande URL för att köra en `GET` begäran.

   <http://localhost:7071/api/message>

   Ett svar returneras, vilket ser ut så här i webbläsaren:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

När du har kontrollerat att funktionen fungerar som den ska kan du anropa API: et från JavaScript-programmet.

### <a name="call-the-api-from-the-application"></a>Anropa API: et från programmet

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Uppdatera filer för att få åtkomst till API: et

1. Uppdatera sedan innehållet i filen _index. html_ med följande kod för att hämta texten från API-funktionen och visa den på skärmen:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Med kärn verktyg som kör använder du tillägget [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code för att hantera filen _index. html_ och öppna den i webbläsaren. 

2. Tryck på **F1** och välj **Live Server: öppna med Live Server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

   > [!NOTE]
   > Du kan använda andra HTTP-servrar eller proxyservrar för att hantera `index.html` filen. Åtkomst till `index.html` från `file:///` fungerar inte.

### <a name="commit-and-push-your-changes-to-github"></a>Genomför och skicka ändringarna till GitHub

Använd Visual Studio Code och genomför och skicka ändringarna till den fjärranslutna git-lagringsplatsen.

1. Tryck på **F1** för att öppna kommando paletten
1. Typ **git: genomför alla**
1. Lägg till ett bekräftelse meddelande
1. Typ i **git: push**

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Skapa en statisk app på Azure Portal-skärm 1":::

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **skapa en resurs**
1. Sök efter **statiska Web Apps**
1. Klicka på **statisk Web Apps (förhands granskning)**
1. Klicka på **skapa**
1. Välj din _Azure-prenumeration_
1. Välj eller skapa en ny _resurs grupp_
1. Namnge appen **My-vanilj-API**.
1. Välj den _region_ som är närmast dig
1. Välj den **kostnads fria** _SKU: n_
1. Klicka på knappen **Logga in med GitHub** och autentisera med GitHub
1. Välj önskad _organisation_
1. Välj **My-vanilj-API** från List rutan _databas_
1. Välj **original** i list rutan _gren_
1. Klicka på **Nästa: bygg >** om du vill redigera build-konfigurationen

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Skapa en statisk app på Azure Portal-skärmen 2":::

Lägg sedan till följande versions information.

1. Ange **./** för _app-platsen_.

1. Ange **API** i rutan _API-plats_ .

   Detta är namnet på den API-mapp som skapades i föregående steg.
   
1. Rensa standardvärdet från _appens artefakt plats_, lämna rutan tom.

1. Klicka på **Granska + skapa**.

| Inställningen | Beskrivning             | Obligatorisk |
| -------- | ----------------------- |
|  App-plats | Platsen för käll koden för det statiska programmet | Yes |
|  API-plats | Platsen för API-backend-servern. Detta leder till rotmappen i Azure Functions-Appaketet | No |
|  Plats för app-artefakt | Platsen för mappen med build-utdata. Vissa JavaScript-ramverk för front-end har ett build-steg som placerar produktions filer i en mapp. Den här inställningen pekar på mappen skapa utdata. | No |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Skapa en statisk app på Azure Portal-skärm 3":::

1. Klicka på **skapa**
1. Vänta tills distributionen är klar (detta kan ta en minut)
1. Navigera till`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Kontrol lera att build-versionen lyckades

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub-arbetsflöde":::

Det distribuerade API: et är tillgängligt på `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub-arbetsflöde":::

Du kan också hitta programmets URL från Azure Portal:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Åtkomst till statisk App-URL från Azure Portal":::

Alternativt kan du direkt komma åt din Azure statiska webbapp på `https://<STATIC_APP_NAME>.azurestaticapps.net` och kontrol lera resultatet i webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill behålla det här programmet kan du använda följande steg för att ta bort Azures statiska webbapp och dess relaterade resurser.

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Skriv **resurs grupper** i det övre Sök fältet
1. Klicka på **resurs grupper** 
1. Välj **myResourceGroup**
1. På sidan _myResourceGroup_ kontrollerar du att de listade resurserna är de som du vill ta bort.
1. Välj **ta bort**
1. Skriv **myResourceGroup** i text rutan
1. Välj **Ta bort**.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera appinställningar](./application-settings.md)
