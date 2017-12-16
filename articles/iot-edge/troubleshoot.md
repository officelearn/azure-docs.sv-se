---
title: "Felsöka Azure IoT-Edge | Microsoft Docs"
description: "Lösa vanliga problem och mer felsökning för Azure IoT kant"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: cb998caf35a9a55ea737cc1a24fbce38aac8abc4
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT kant

Om du får problem med att köra Azure IoT kant i din miljö, Använd den här artikeln som vägledning för felsökning och upplösning. 

## <a name="standard-diagnostic-steps"></a>Standard diagnostiken 

När det uppstår ett problem, mer information om tillståndet för din IoT-Edge-enhet genom att granska loggarna för behållaren och meddelanden som skickar till och från enheten. Använda kommandon och verktyg i det här avsnittet för att samla in information. 

* Granska loggarna för docker-behållare för att identifiera problem. Börja med distribuerade behållarna titta sedan på de behållare som utgör IoT kant-körning: Edge Agent och kant-hubb. Edge Agent-loggarna ger vanligtvis info på lifecylce för varje behållare. Edge-hubb-loggarna ger information på meddelanden och routning. 

   ```cmd
   docker logs <container name>
   ```

* Visa meddelanden som skickas via navet kant och samla insikter om uppdatering av enheter egenskaper med utförlig loggar från runtime-behållare.

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* Om du får problem med nätverksanslutningen inspektera din edge enheten miljövariabler som enheten anslutningssträngen:

   ```cmd
   docker exec edgeAgent printenv
   ```

Du kan också kontrollera de meddelanden som skickas mellan IoT-hubb och kant för IoT-enheter. Visa dessa meddelanden med hjälp av den [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) tillägget för Visual Studio-koden. Mer information finns i [praktiskt verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

När du undersöker loggar och meddelanden för information kan du även försöka att starta om Azure IoT kant-körning:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge agenten stoppar efter ungefär en minut

Edge-agenten startar och har körts för ungefär en minut sedan stoppar. Loggarna indikera att Edge-Agent försöker ansluta till IoT-hubb via AMQP och sedan cirka 30 sekunder senare försöker ansluta med AMQP över websocket. När det inte går avslutas Edge-agenten. 

Exempel Edge agenten loggar:

```
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Rotorsaken
Nätverkskonfigurationen på värdnätverket förhindrar att Edge-agenten ansluter till nätverket. Agenten försöker först ansluter via AMQP (port 5671). Om detta misslyckas försöker websockets (port 443).

IoT-Edge runtime ställer in ett nätverk för varje moduler att kommunicera på. Det här nätverket är på Linux, en brygga för nätverk. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter med hjälp av Windows-behållare som använder NAT-nätverk. 

### <a name="resolution"></a>Lösning
Kontrollera att det finns en väg till internet för IP-adresser som tilldelats den här bridge/NAT-nätverk. Det har förekommit fall där en VPN-konfiguration på värden åsidosätter IoT kant-nätverk. 

## <a name="edge-hub-fails-to-start"></a>Edge-hubb som inte går att starta

Edge hubb går inte att starta och skriver ut följande meddelande till loggarna: 

```
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Rotorsaken
En annan process på värddatorn har bunden port 443. Edge hubben mappar portar 5671 och 443 för att använda i gateway-scenarier. Den här portmappning misslyckas om en annan process har redan bundits till den här porten. 

### <a name="resolution"></a>Lösning
Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge-agenten kan inte komma åt en modul bild (403)
En behållare som inte kan köras och Edge Agent loggarna visar ett 403-fel. 

### <a name="root-cause"></a>Rotorsaken
Edge-agenten har inte behörighet att komma åt en modul avbildningen. 

### <a name="resolution"></a>Lösning
Försök med att köra den `iotedgectl login` kommandot igen.

## <a name="next-steps"></a>Nästa steg
Tror du att du har hittat ett programfel i IoT kant-plattformen? Gör [skicka in ett ärende](https://github.com/Azure/iot-edge/issues) så att vi kan fortsätta att förbättra. 
