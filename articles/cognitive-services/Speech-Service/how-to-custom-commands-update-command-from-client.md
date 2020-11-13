---
title: Uppdatera ett kommando från klient programmet
titleSuffix: Azure Cognitive Services
description: uppdatera ett kommando från klient programmet
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 290f9ee9c23071ac56b1ff0c65ddc03decbc7344
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571279"
---
# <a name="update-a-command-from-the-client"></a>Uppdatera ett kommando från klienten

I den här artikeln får du lära dig hur du uppdaterar ett pågående kommando från ett klient program.

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Uppdatera status för ett kommando

Om klient programmet kräver att du uppdaterar statusen för ett pågående kommando utan röst inläsning kan du skicka en händelse för att uppdatera kommandot.

För att illustrera det här scenariot kan vi skicka följande händelse aktivitet för att uppdatera statusen för ett pågående kommando (TurnOnOff). 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Tillåter granskning av de viktigaste attributen för den här aktiviteten.

| Attribut | Förklaring |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **bastyp** | Aktiviteten är av typen "event" och namnet på händelsen måste vara "RemoteUpdate". |
| **värde** | Attributet "value" innehåller de attribut som krävs för att uppdatera det aktuella kommandot. |
| **updatedCommand** | Attributet "updatedCommand" innehåller namnet på kommandot, "updatedParameters" är en karta med namnet på parametrarna och deras uppdaterade värden. |
| **Avbryt** | Om det pågående kommandot måste avbrytas anger du attributet "Cancel" till true. |
| **updatedGlobalParameters** | Attributet "updatedGlobalParameters" är också en mappning, precis som "updatedParameters", men används för globala parametrar. |
| **processTurn** | Om turn måste bearbetas efter att aktiviteten har skickats anger du attributet "processTurn" till true. |

Du kan testa det här scenariot i portalen med anpassade kommandon.

1. Öppna programmet Anpassade kommandon som du skapade tidigare. 
1. Klicka på träna och testa.
1. Skicka "Vänd".
1. Öppna sido panelen och klicka på aktivitets redigeraren.
1. Skriv och skicka RemoteCommand-händelsen som anges i föregående avsnitt.
    > [!div class="mx-imgBorder"]
    > ![Skicka fjärrkommando](media/custom-commands/send-remote-command-activity.png)

Observera att värdet för parametern "mikrofonen" har angetts till "on" med hjälp av en aktivitet från klienten i stället för tal eller text.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Uppdatera katalogen för en-parameter för ett kommando

När du konfigurerar en lista över giltiga alternativ för en parameter definieras värdena för parametern globalt för programmet. 

I vårt exempel har SubjectDevice-parametern en fast lista med värden som stöds, oavsett konversationen.

Om du behöver lägga till nya poster i parameterns katalog per konversation kan du skicka följande aktivitet.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Med den här aktiviteten har vi lagt till en post för "stereo" till katalogen för parametern "SubjectDevice" i kommandot "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Uppdaterings katalog":::

Observera några saker.
1. Du behöver bara skicka den här aktiviteten en gång (helst direkt efter att du har startat en anslutning).
1. När du har skickat den här aktiviteten bör du vänta tills Event ParameterCatalogsUpdated skickas tillbaka till klienten.

## <a name="add-additional-context-from-the-client-application"></a>Lägg till ytterligare kontext från klient programmet

Du kan ställa in ytterligare kontext från klient programmet per konversation som senare kan användas i programmet för anpassade kommandon. 

Tänk dig till exempel om det scenario där du vill skicka ID och namn på enheten som är ansluten till programmet för anpassade kommandon.

För att testa det här scenariot ska vi skapa ett nytt kommando i vårt aktuella program.
1. Skapa ett nytt kommando med namnet GetDeviceInfo.
1. Lägg till en exempel mening med "Hämta enhets information".
1. I slut för ande regeln "klar" lägger du till en skicka röst svars åtgärd.
    > ![Skicka tal svar med kontext](media/custom-commands/send-speech-response-context.png)
1. Spara och träna ditt program.
1. Testa ditt program.
    > ![Skicka klient kontext aktivitet](media/custom-commands/send-client-context-activity.png)

Observera några saker.
1. Du behöver bara skicka den här aktiviteten en gång (helst direkt efter att du har startat en anslutning).
1. Du kan använda komplexa objekt för ClientContext.
1. Du kan använda ClientContext i tal svar för att skicka aktiviteter och när du anropar webb slut punkter.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppdatera ett kommando från en webb slut punkt](./how-to-custom-commands-update-command-from-web-endpoint.md)
