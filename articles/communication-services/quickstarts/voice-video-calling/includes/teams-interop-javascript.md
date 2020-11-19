---
title: Snabb start – Anslut till ett team möte
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ab13e02c9154dbfc1bd28872f1bd6f993019a604
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915447"
---
## <a name="prerequisites"></a>Förutsättningar

- En fungerande [kommunikations tjänst som anropar en app](../getting-started-with-calling.md).
- En [team distribution](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Aktivera team samverkan

Funktionen Teams driftskompatibilitet finns för närvarande i privat för hands version. Om du vill aktivera den här funktionen för din kommunikations tjänst resurs, kan du skicka e-post [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) till:

1. Prenumerations-ID för den Azure-prenumeration som innehåller kommunikations tjänst resursen.
2. Ditt teams klient-ID. Det enklaste sättet att skaffa detta är att [Hämta och dela en länk till teamet](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Du måste vara medlem i den ägande organisationen av båda entiteterna för att kunna använda den här funktionen.

## <a name="add-the-teams-ui-controls"></a>Lägg till Team UI-kontroller

Lägg till en ny text ruta och knapp i HTML-koden. Text rutan används för att ange team Mötes kontexten och knappen kommer att användas för att ansluta till det angivna mötet:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivera UI-kontroller för team

Nu kan vi binda **Mötes knappen Anslut till Team** till den kod som ansluts till de tillhandahållna teamen:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Hämta Mötes kontexten

Team-kontexten kan hämtas med Graph API: er. Detta beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

Du kan också hämta nödvändig Mötes information från URL: en **till** mötets inbjudan i mötesinbjudan.

## <a name="run-the-code"></a>Kör koden

WebPack-användare kan använda `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera din program värd på en lokal webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Skärm bild av det färdiga JavaScript-programmet.":::

Infoga team-kontexten i text rutan och tryck på *Anslut Teams möte* för att ansluta till teamen i kommunikations tjänst programmet.